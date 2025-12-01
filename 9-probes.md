####What is probes?
- Probes in k8s are health  checks for containers to verify if the container is healthy or not
- types of probes:
  - liveness probe: to check whether the pod is alive or dead (if not alive then restart the pod)
  - readiness probe: to check whether the pod is ready to serve the traffic (if not remove from service endpoint), Service Endpoint = The list of ACTIVE POD IPs that receive traffic from a Service., Removing from service endpoint = Removing from the routing table/IP table
  - startup probe: to check whether the application inside the pod is started or not ( if not started then kill the pod and restart),  it is not using now (legacy probe)
