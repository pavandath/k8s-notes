
 #### 1. Namespaces
- Take an apartment every apartments has families and for each family there will be individual flats.
- Similarly, each cluster will be applications and each application will be having individual name servers
- Each application will be having it's own name space
- Namespace is nothing but dividing a logical boundary inside the cluster.
- Each **Application** has its own **Namespace** — a logical boundary that separates its resources (pods, services, name servers, etc.) from others.
- The **Namespace** ensures isolation, so one application’s resources or configurations don’t interfere with another’s — just like one family can’t access another family’s flat.
- After creating there won't be particular location where it is created

			kubectl get ns
			kubectl get namespaces
			kubectl get pods -n kube-system
			kubectl create ns test-ns (imperative way)
			yaml file(declerative way)
			apiVersion: v1
			- kind: Namespace
			metadata:
				name: test-ns
			
		kubectl api-resources - This gives all resources either it is namespace scoped or cluster scoped
		

#### 2. Pod

- Kubernetes can't deploy containers directly on worker nodes instead these containers are encapsulated as pod.
- A **Pod** is the **smallest deployable unit** in Kubernetes.
- - A **Pod** runs **one or more containers** (usually Docker containers).  
- All containers inside a pod:
    - Share the same **network (IP address)**
    - Share the same **storage volumes**
    - Can easily communicate with each other using `localhost`
- But no two containers inside a pod have same port numbers
- In Kubernetes, pods have a **1:1 relationship with scaling units**.  
- If a pod (`p1`) contains multiple containers and you scale that pod, Kubernetes will create **new pod replicas** (`p2`, `p3`, etc.) — each with the **same set of containers** inside.  
- The **containers inside a single pod are not scaled individually**.

			kubectl run <pod-name> --image <image-name>
			kubectl run new-nginx --image nginx
			kubectl create -f <file.yaml>
			kubectl get pods -n <namespace>
			kubectl describe pod <podname>
			kubectl get pods -o wide (Tells which node it deployed)
			kubectl logs <pod-name>
			kubectl exec -it <pod-name> -- /bin/bash
			kubectl delete pod <pod-name>
			kubectl delete -f <file.yaml>



#### 3. ReplicaSet

- A ReplicaSet in Kubernetes is a controller that ensures a specified number of identical Pod replicas are running at all times within a cluster.
- It will be ensuring that the desired number of identical pods are always running

- Pods in k8s will be identified by Labels
- We provide those labels to ReplicaSet in the form of 
	kubectl run my-pod --image nginx --labels "app=frontend,env=production"
	kubectl get pods --show-labels
- If you want to attach a label to the existing pod then:
			kubectl label pod <pod-name> version=v1
- If you want  to overwrite the labels you cannot change the key but if you want to change the value then:
			kubectl label pod <pod-name> version=v2 --overwrite
- In order to remove label use - at the end of the label
			 kubectl label pod my-pod version-
- In order to filter the pods based on variables
			 kubectl get pods -l app (use -l)

				kubectl get rs

- Converting commands to yaml file
kubectl run bangalorepod --image=nginx --labels="env=nondev, tier=frontend, dc=chen" --dry-run -o yaml > pod.yaml


#####Difference Between Kubectl create vs apply
- Create is used when we are creating for the first time
- apply is used whenever we updated the file and need those changed to reflect
- apart from that both serves the same
- But the existing pods dont get updated, after deleting the existing pod and whenever a new pod spins up the latest changes will be inherited by  pod this concept is in replicaset.
- And inorder to overcome this k8s has an object known as deployment
- In deployment everything is same as ReplicaSet only kind will be changed
- Also deployment has an advantage Roll back to the previous version

		![[Pasted image 20251029150025.png]]

	 **nginx-deployment-78c4c976b9-9zh9x :**		
	 nginx-deployment : It is the Deployment name
	 78c4c976b9 :  ReplicaSet name
	 9zh9x: Pod name
	- kubectl get deploy 
- We can scale the pods without needing to change the yaml files using the below command:
			- kubectl scale deployment/nginx-depoyment --replicas 6
- If we want to change image in the same way then:
			- kubectl set image deployment/nginx-deployment dev-container=devopswithcloud/nginx:orange
- But this is not the right way, why because if someone rerun the yaml file again the settings will all be overridden again
- Here kubernetes will follow rolling update strategy when creating new replicaset i.e., It will create a new replicaset and remove one pod from old rs and add one pod in the new rs, it doesn't follow the recreate strategy where it will remove then entire pods and create the new pods.
- rolling update is the default mode but we can change it if we want to 



####Ways to update deployment

- I can modify directly in file
- I can directly modify the deployment using the command:
		- kubectl edit deploy <deployment-name>
		- You can directly modify the object
- And using set image
	- kubectl set image deployment/<deployment-name> <container-name>=<updated-image-name:tag>
#####Pausing the deployment
- For it to know options for history,resume,restart etc we use below command:
			- kubectl rollout
			- kubectl rollout pause deployment/<deployment-name>
			- kubectl rollout history deployment/<deployment-name>
			- This will be helpful for seeing updated changes

##### In order to roll-back to previous verision:
-  kubectl rollout history deployment/<deployment-name>
- This will show all the history steps
- kubectl rollout undo deployment/<deployment-name>
- This will go one step back to history
- ![[Pasted image 20251030125437.png]]
- if we want two steps  back or to a particular history then:
-  kubectl rollout undo deployment/<deployment-name> --to-revision=1







