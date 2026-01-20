# QoS in Kubernetes

Quality of Service determines pod priority when Kubernetes needs to free up resources on a node. Check with:

```bash
kubectl describe pod <pod-name>
# Look for: QOS Class: <Type>
```
Three QoS Classes:

1. BestEffort - LOWEST Priority

When: Pod has NO requests AND limits (both CPU & memory)

Priority: Lowest

Kill Order: First to be killed when node needs resources

Resource Guarantee: None

Could get: Zero resources if node is full

2. Burstable - MEDIUM Priority

When: Pod has requests AND limits where limit > request

Priority: Medium

Kill Order: Killed after BestEffort pods

Resource Guarantee: Minimal guarantee (gets at least requested amount)

3. Guaranteed - HIGHEST Priority

When: Pod has requests = limits (for both CPU & memory)

Priority: Highest

Kill Order: Last to be killed

Resource Guarantee: Full guarantee (gets exactly requested amount)
