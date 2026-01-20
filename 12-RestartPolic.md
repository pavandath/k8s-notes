# Restart Policies

Controls when Kubernetes restarts containers in a pod.

* **always (recommended way)** - default - this allows k8s to restart the container if an issue occurs
    * if the container ask is to print hello world and gets killed it will not be known for k8s so it will restart the container
    * always is a recommended way because hello world type applications should not be placed in the pod in the first place 

* **on failure** - k8s will restart the container only if there is any error code generated in the container

* **never** - Never restarts (no matter what happens)
