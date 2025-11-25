- So the disadvantage of the Loadbalancer is for each micro service we need to assign a loadbalancer and to manage all the loadbalancers we need to assign one more laodbalancer which increases cost and complexity.
- We can overcome with the ingress controller, it is also an k8s api object used to enable external access to the services that are within the cluster.
- We can configure both http and https.
- Traffic first reaches the **Ingress Controller**, which then forwards the request to the appropriate **Service**, and the Service finally routes it to the correct **Pod**. This works even if there is only one microservice.

- When there are multiple microservices, each microservice will have its own Kubernetes **Service**, and the Ingress Controller sits on top of all of them. It uses **domain-based** or **path-based routing** (for example, `/cart` → Cart service) to direct traffic to the right microservice.

- The Ingress Controller also supports **TLS termination**, meaning HTTPS certificates can be configured and managed directly at the Ingress level instead of for each individual service.
- Ingress and Ingress controller both are different
- 