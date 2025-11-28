- If we want to deploy a pod on one particular node then we will use advance scheduling
- Then if that pod is deployed on other nodes then it wont work
- If we give pod to run on node 4 but bode doesn't exists then pod will be in pending state

### Why Pods Fail on Wrong Nodes

| Failure Scenario | Consequences |
|------------------|-------------|
| **Hardware Dependencies** | GPU workloads without GPU access |
| **Storage Requirements** | Local volume paths not available |
| **Network Constraints** | Specific NICs or IP configurations missing |
| **Licensing Restrictions** | Node-bound software licenses violated |
| **Performance Requirements** | Insufficient CPU/Memory resources |


#### Advance scheduling Cocnceps
- Node name
- Node selector
- node affinity or anti affinity
- taints and tolerations


**1 Node Name**:
- If we want to schedule a pod to a specific node we can use nodeName field in the pod spec.
- Mentioning  node name is  not correct because if node is not having capacity then it will be pending state
- To overcome this we use nodeSelector


**2 Node Selector**:
- Where we can specify a label to select the node and mention that label so that pod will be scheduled to that node
- There will be no problem if the node stopped and new node is created with different name but with same label 
- the pod will be scheduled to that node
- But if there are 4 nodes w1 is large w2 and w3 are small and w4 is medium and we want to schedule the pod on medium or large node then it is not enough to use nodeSelector
- To overcome this we use node affinity and anti affinity concept

**3 Node Affinity**:
- If we want to schedule the pod on medium or large node whiic means we need expressions to select the nodes based on labels for that we use node affinity
- simply node affinity is an advanced version of node selector and it supports operators like In, NotIn, Exists, DoesNotExist, Gt, Lt
- **In**: 
- If there is a key disktype=ssd, hdd, nvme 
- And we want to schedule on ssd then we can use In operator it simply sees if any key has ssd value then schedule the pod on that node and it must include ssd among the values
- **NotIn**:
- If we want to avoid scheduling on hdd then we can use NotIn operator it simply sees if any key has hdd value then it will not schedule the pod on that node and it must not include hdd among the values
- **Exists**:
- If we want to schedule the pod on any node which has disktype key irrespective of its value then we can use Exists operator
- If the key is disktype and we dont care about its value then we can use Exists operator
- **DoesNotExist**:
- If we want to avoid scheduling the pod on any node which has disktype key irrespective of its value then we can use DoesNotExist operator
- **Greaterthan**:
- If we want to schedule the pod on any node which has a key cpu with value greater than 16 then we can use Gt operator
- for example if cpu=32 it will schedule the pod on that node
- **Lessthan**:
- If we want to schedule the pod on any node which has a key cpu with value less than 16 then we can use Lt operator
- for example if cpu=8 it will schedule the pod on that node

**NOTE:** Scheduling is done under 2 factors and we cant change it scheduler is built like that those are **filtering and scoring**
- In filtering phase scheduler filters the nodes based on the requirements specified in the pod spec like nodeSelector, node affinity, taints and tolerations etc., and creates a list of feasible nodes
- In scoring phase scheduler ranks the feasible nodes based on various criteria and selects the best node to schedule the pod

- Node affinity has two modes: 
- Hard rules : mandatory rules
      - requiredDuringSchedulingIgnoredDuringExecution
      - There must be a label or expression to match during the scheduling and  ignore after that i.e, during execution
      - it is mandatory to be an expression if it doesnt exist pod will be in pending state but doesnt execute 
- Soft rules : preferred rules  
      - preferredDuringSchedulingIgnoredDuringExecution
      - There may be a label or expression to match during the scheduling and  ignore after that i.e, during execution
      - it is not mandatory to be an expression if it doesnt exist pod will be scheduled on some other node

**NOTE:**: IgnoredDuringExecution means if node labels change after the pod is running, the pod won't be evicted/moved. 

**Interview question:**

- We are having 4 worker nodes w1, w2, w3, w4
- and there is a pod which can  be deployed anywhere 
- Now we have tell the pod that to go and deploy where nodeSelector colour=blue
- Now where will the pod go?
- It will be in pending state because none of the nodes are having that label
- now Imagine w1 is a high costly machine wi app=ml label
- Now all the pods without nodeSelector will go and deploy to w1 because it is high end machine
- Now the an actual pod  with with nodeSelector app=ml came but it is in pending state because w1 is already full with other pods
- Now we need a solution for this, now we need a solution where nodes can be having their own priorities.  
- Solution is to use Taints and Tolerations
- - taints will be applicable at node level
- tolerations will be applicable at pod level
- tolerations that are having colour blue can be scheduled to the node which is having blue taint and that node will not accept any other pod which is not having blue toleration
