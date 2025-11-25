- So generally we need a disk for logs and property files but for a minimum size of the disk for creation must be 15Gb or some cases in TB but these files are in kb and mbs so in order to save money this concept of buckets came where we can use buckets to store these files and pay for only how much storage we used
- When we are running Application in k8s , if we want to pass dynamic values during the run time then k8s provides two options:
				- config_map
				- secrets
- A **ConfigMap** in Kubernetes is an object used to **store non-sensitive configuration data** (like environment variables, property files, JSON/YAML configs, etc.) so that you don’t have to hard-code them inside container images.

---

## ✅ Why ConfigMap exists

Without ConfigMaps, you would need to:

❌ Rebuild Docker image every time a config changes  
❌ Hard-code values inside code or environment variables  
❌ Store config files inside the container filesystem

With ConfigMaps:

✅ You store config **outside the container**  
✅ You can update config without rebuilding images  
✅ Same container image can run in dev/stage/prod with different configs

- so if the configuration later changes changing the config map or creating the new one can solve it instead of rewritting the image
- kubectl get cm - to get all config maps
- kubectl get secrets
- Refer: https://github.com/devopswithcloud/KubernetesRepo/blob/main/V2/09-ConfigMap_Secrets/1-cm-secret.md
- I can assign config map data into volume and assign it to the pod
- kubectl describe cm <config-name>
		- It will show the data 
- kubectl describe secret db-secret 
		-  It will only show key and value in bytes
- kubectl get secret db-secret -o yaml
		- It will take the values as random not the given once
- while writing secrets imperative k8s will automatically encode and decode it:
			- kubectl create secret generic db-secret \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASS=pa55w0rd
- But while writing the yaml you must encode it and write 
		- apiVersion: v1
		kind: Secret
		metadata:
		  name: db-secret
		type: Opaque
		data:
		  DB_USER: YWRtaW4=       # base64 encoded 'admin'
		  DB_PASS: cGE1NXcwcmQ=   # base64 encoded 'pa55w0rd'
- 