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

