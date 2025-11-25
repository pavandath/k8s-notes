Since containers are ephemeral once it stopped it's data is lost so in-order to keep the storage persistent we use volumes.

- There are 4 different types of volumes in k8s:
	- Persistent volumes
	- Persistent Volume claim
	- Storage class
	- Dynamic provisioning
### 2. **hostPath**

- Maps a **path from the host node** into the Pod.
    
- Useful for accessing files on the node (e.g., `/var/logs`)
- When you use a **`hostPath` volume**, Kubernetes mounts a **directory from the specific worker node’s filesystem** into the Pod.
- If your Pod is scheduled on **Node A**, it uses `/data/app` **on Node A**.  
So:

- ✅ If the Pod **restarts** on the same node → data **persists** (because `/data/app` is still there).
    
- ❌ If the Pod is **rescheduled** to another node (Node B) → the new node’s `/data/app` will be **empty** (or might not even exist).
    

That’s because **`hostPath` is node-local storage** — not shared across nodes.

| Step | Action                                      | Node   | What Happens                                            |
| ---- | ------------------------------------------- | ------ | ------------------------------------------------------- |
| 1    | You create a Pod using hostPath `/data/app` | Node A | Data stored at `/data/app` on Node A                    |
| 2    | Node A crashes                              | -      | Pod dies                                                |
| 3    | Pod rescheduled automatically               | Node B | `/data/app` on Node B is empty → previous data **lost** |
- **data can’t be linked or recovered** automatically if the Pod moves to a new node.


### 3. **PersistentVolume (PV) & PersistentVolumeClaim (PVC)**

Used for **persistent data beyond Pod lifecycle** — perfect for databases or long-term storage.

#### 🧩 PersistentVolume (PV)

- Represents actual storage (EBS, NFS, GCE disk, etc.).
    
- Created by the admin or dynamically by Kubernetes.
    

#### 🧩 PersistentVolumeClaim (PVC)

- A request by a user (Pod) for storage.
    
- Kubernetes binds PVC to an appropriate PV.

- In pVC the disk is segregated and creates a layer as pv and around the pod a pvc layer is created and this pvc and pv layer is linked rewrite this sentance into neat understandable way
						- or
- In a PVC setup, the storage disk is represented as a PersistentVolume (PV), while the PersistentVolumeClaim (PVC) acts as a layer that connects the Pod to that storage. The PVC serves as a bridge, linking the Pod with the underlying PV to provide persistent storage.

> When creating a PersistentVolume (PV), there are three access modes: **RWO**, **ROX**, and **RWX**.
> 
> - ** RWO (ReadWriteOnce)** – Only a single Pod can mount the volume with read and write access.
>     
> -  **ROX (ReadOnlyMany)** – Multiple Pods can mount the volume, but only with read-only access.
>     
> -  **RWX (ReadWriteMany)** – Multiple Pods can access the volume at the same time with both read and write permissions.


	![[Pasted image 20251104122823.png]]
- No one can use the pv if it is already binded by pvc unless it is released if pv is 10 gb and pvc is 8 gb then 2 gb is wasted

- in order to implement this we need to create an nfs server because if we attatch a disk to one node and if the pod is created in another then the data won't be there.
- using nfs server so technically what happening here means in w1 node if a data is modified it will be updated in the nfs server and that is updated in w2 node everything will be synced so no matter where the pod is created there won't be an issue
- You _can_ use NFS with `hostPath`, but it is **not Kubernetes-managed**, not scalable, and not production-safe.  
> The **real advantage of NFS as a PV** is that Kubernetes handles mounting, scheduling, provisioning, and lifecycle — no manual node setup required.

persistentVolumeReclaimPolicy: Recycle means:

  

When the PVC is deleted, Kubernetes will automatically clean the data inside the volume by running a command similar to:

  

rm -rf /path/to/storage/*

  

After wiping the data, the PV becomes Available again for another claim.

  

It does NOT delete the actual directory or disk, it only deletes the files inside it.

  

| Reclaim Policy           | What happens to PV?         | What happens to data inside storage?                                 |

| ------------------------ | --------------------------- | -------------------------------------------------------------------- |

| **Retain**               | PV goes to `Released` state | ✅ Data is **kept** (not deleted)                                     |

| **Delete**               | PV object deleted           | ❗ Data is deleted **only if backend supports it** (EBS, GCE PD etc.) |

| **Recycle** (deprecated) | PV reused                   | ❌ Data wiped via `rm -rf` (not supported anymore)                    |



####Dynamic Provisioning
- Dynamic provisioning like not all these steps if you want 10 gb take 10gb disk if you want it to be 10 to 20 make it no more manual only automation
You stop pre-creating PVs. Instead:

1. You define a **StorageClass** (points to a CSI driver, e.g., AWS EBS, GCE PD, Azure Disk, Longhorn, Ceph, NFS provisioner, etc.).
    
2. Your **PVC** asks for what it needs (size, access mode, class).
    
3. Kubernetes + the CSI driver **automatically creates a matching PV** on demand.
    
4. (Optionally) you can **resize** later if the class allows it.

### What you get

- **No manual PVs**
    
- **Exact sizes** (ask for 10Gi → get 10Gi)
    
- **Auto-delete** when PVC is deleted (if `reclaimPolicy: Delete`)
    
- **Optional expansion** (e.g., 10Gi → 20Gi) with `allowVolumeExpansion: true`

**NOTE:** It must be supported by clusters kubeadm like underlying cluster dont support it so we need manage cluster like GKE.

