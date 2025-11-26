#### 5. Service
- Pods will be having ip's but these are not static, once the pods get restarted those ip's will change.
- So in order to pods to have static ip's we use service.
- There will be two ports container port and node port same as docker if we provide docker port then it will assign or else it will automatically assign a random port
- There will be node port, cluster ip and loadbalancers in service
- The node port indicates that this is a port at the node where allocating a port to the pod so that if someone access thorugh this port it will redirect them to it.
- NodePort makes an internal pod accessible from outside the cluster by opening a specific port on every node, which then forwards traffic to the pod."
			![[Pasted image 20251031165018.png]]
- if service port is not mentioned then it will consider container port as service port.
- Cluster ip means communicating between pods internally inside the cluster without hitting outside internet.


Let’s assume you have a **Pod** running a **container** exposing port `8080`.

Let’s assume you have a **Pod** running a **container** exposing port `8080`.

Now, you create a **Service** (like `NodePort`) to expose that Pod.
### ⚙️ Port Mapping (step-by-step)

|Level|Term|Example Port|Description|
|---|---|---|---|
|**Container**|**TargetPort**|`8080`|The port **inside the Pod** where the application actually runs.|
|**Service**|**Port**|`80`|The port **exposed by the Service** internally within the cluster. This is the “entry point” to reach your Pod.|
|**Node**|**NodePort**|`30080`|The port **on every worker node’s IP**. Accessing `<NodeIP>:30080` will send traffic into the Service.|


User → NodeIP:30080 (NodePort)
       ↓
Service Port 80 (ClusterIP)
       ↓
Pod TargetPort 8080 (Container)
✅ **Service acts like a middleman** (load balancer + router).  
✅ It maps **NodePort → Service Port → TargetPort.**  
✅ The **NodePort** is open on **all nodes** in the cluster.  
✅ When a request hits the NodePort, Kubernetes routes it to one of the matching Pods behind that Service.
kubectl describe svc python-nodeport-service  

- Here you can see 
- Endpoints:                192.168.190.69:8080,192.168.80.198:8080
- These are pods that are created in the backed 

- Loadbalancer cant be created in underlying(kubeadm) cluster because it dont have ability to communicate with the gcp. For that you have to use managed kubernetes cluster

######Note: In real time only they use clusterIP
- why dont we use loadbalancers means consider there are 5 microservices and for each microservice we need to assign a loadbalancer and on top of all 5 loadbalancers we need to assign another loadbalacer to handle where it increases cost, complexity and not needed that much.
- So on top of all micro services we will establish a ingress controller

I created a pod and it is created in workernode 1 but I accessed it using worker node 2 ip:node port I can access it how

###### ChatGPT said:

That’s exactly how **NodePort** works 🙂

When you create a **Service type: NodePort**, Kubernetes:

- **Reserves the same port on every node** (e.g., 30xxx) via **kube-proxy**.
    
- Programs routing rules so that **any node’s** `NODE_IP:NODEPORT` will **forward to one of the Service’s Pods**, no matter which node those Pods are actually on.
    

So even if your Pod is running only on **worker-node-1**, hitting:

`http://worker-node-2:<nodePort>`

will be accepted on node-2 and **proxied across the cluster network** to the Pod on node-1.

A quick mental model:

`Client -> node2:NodePort  --(kube-proxy)-->  Service  --(cluster network)-->  Pod on node1`

### Extra tips

- By default, Services use `externalTrafficPolicy: Cluster`, which allows this cross-node forwarding.
    
- If you set `externalTrafficPolicy: Local` on a **NodePort/LoadBalancer** Service, traffic on a node will only go to **Pods local to that node** (useful to preserve client IP or avoid cross-node hops).
    
- Under the hood, kube-proxy (iptables/IPVS) installs rules on **every node** to make this happen.
    

So what you observed is the expected, normal behavior for NodePort.

Client → Node IP (any worker) : NodePort
        ↓
    kube-proxy on that node
        ↓
    Service (virtual IP & port inside cluster)
        ↓
    Selects a matching Pod (via label selector)
        ↓
    Sends traffic to the Pod's targetPort (containerPort)

**NOTE:**
#####How Clusterip works?
- "In Kubernetes, pods have dynamic IP addresses that change whenever they restart or get recreated. This creates a major problem for inter-pod communication since you can't rely on stable IP addresses. The solution is Kubernetes Services, which provide a permanent, stable endpoint. When Pod A needs to communicate with Pod B, it simply uses the service name and port (e.g., blue-service:80) instead of direct pod IPs.

- Here's the magic of HOW it works: When a pod calls blue-service:80, Kubernetes DNS resolves it to a virtual ClusterIP (like 10.96.x.x), but this IP doesn't actually exist on the network. Instead, iptables/ipvs rules on the node intercept the request and redirect it directly to one of the healthy pod IPs behind the service. The ClusterIP acts only as a discovery mechanism, while the actual communication happens as direct pod-to-pod traffic.

- Behind the scenes, the service automatically tracks all current pod IPs through continuous endpoint updates. This abstraction layer ensures that even though individual pods come and go with changing IPs, the communication between services remains reliable and consistent through this intelligent interception and routing system."


