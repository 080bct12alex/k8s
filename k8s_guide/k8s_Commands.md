# Kubernetes (kubectl) Commands

[ Minikube Installation Guides](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)

**1. Check Kubernetes Cluster Version**

```bash
kubectl version
```

2. Get All Resources in the Cluster

- Get a summary of all resources in the Kubernetes cluster (Pods, Deployments, Services, etc.).

```bash
kubectl get all
```

**3. View Running Pods**

```bash
kubectl get pods
```

**4. View All Resources (Pods, Services, Deployments) in a Namespace**

- List all resources within a specific namespace (e.g., default).

```bash
kubectl get all -n <namespace>
```

**5. Create a Resource from a YAML File**

- Create resources (such as config,secrets,Pods, Deployments, Services) from a YAML file.

```bash
kubectl apply -f <resource-file>.yaml
```


**6. Check Resource Status**

Check the status of a specific resource (like a Deployment, Service, or Pod).

```bash
kubectl get <resource-type>
```


**7. Describe a Resource**

- Get detailed information about a specific resource (Pod, Deployment, Service).

```bash
kubectl describe <resource-type> <resource-name>
```

= Example:

```bash
kubectl describe pod my-pod
```

**8. Delete a Resource**

- Delete a resource such as a Pod, Deployment, or Service.

```bash
kubectl delete <resource-type> <resource-name>
```

- Example:
```
kubectl delete pod my-pod
kubectl delete deployment my-deployment
```

**9. Scale a Deployment**

- Scale a deployment to a specific number of replicas.

```bash
kubectl scale deployment <deployment-name> --replicas=<number>
```

- Example:
```
kubectl scale deployment my-deployment --replicas=3
```

**10. Get Pod Logs**

- Fetch logs for a specific pod.

```bash
kubectl logs <pod-name>
```

- Example:
```
kubectl logs my-pod
```

**11. Execute a Command Inside a Pod**

- Execute a command inside a running pod (e.g., to debug).

```bash
kubectl exec -it <pod-name> -- /bin/bash
```


**12. View Namespaces**

- List all namespaces in the Kubernetes cluster.

```bash
kubectl get namespaces
```

**13. Create a Secret**

- Create a Kubernetes secret from literal values (e.g., for sensitive data).

```bash
kubectl create secret generic <secret-name> --from-literal=<key>=<value>
```

- Example:
```
kubectl create secret generic my-secret --from-literal=API_KEY=abcdef123
```

**14. Create a ConfigMap**
- Create a Kubernetes ConfigMap from literal values or a file.

```bash
kubectl create configmap <configmap-name> --from-literal=<key>=<value>
```

- Example:
```
kubectl create configmap my-config --from-literal=app_mode=production
```

**15. Apply Multiple YAML Files**

- Apply multiple Kubernetes resource files in a single command.

```bash
kubectl apply -f <file1>.yaml -f <file2>.yaml
```

**16. Port Forward to Access a Pod Locally**

- Port-forward a port from a Pod to your local machine.

```bash
kubectl port-forward pod/<pod-name> <local-port>:<container-port>
```

- Example:
```
kubectl port-forward pod/my-pod 8080:80
```

**17. Get Cluster Information**

- Get details about your Kubernetes cluster.
```
kubectl cluster-info
```

**18. Get Deployment Details**

- Get information about deployments in a specific namespace.

```bash
kubectl get deployments -n <namespace>
```

- Example:
```
kubectl get deployments -n default
```

**19. Update Resource with New Config**

- Update an existing resource (e.g., Deployment) by changing its configuration.

```bash
kubectl apply -f <updated-resource-file>.yaml
```

**20. Check Resources with Specific Labels**

- Filter resources by labels.

```bash
kubectl get <resource-type> -l <label-key>=<label-value>
```

- Example:
```
kubectl get pods -l app=my-app
```

21. Delete All Resources in a Namespace

- Delete all resources (pods, deployments, services, etc.) within a namespace.

```bash
kubectl delete all --all -n <namespace>
```

- Example:
```
kubectl delete all --all -n default
```


**22. Expose a Deployment via Service**

- Create a service to expose a deployment (e.g., expose on port 8080).

```bash
kubectl expose deployment <deployment-name> --type=LoadBalancer --port=80 --target-port=8080
```

- Example:

```bash
kubectl expose deployment my-deployment --type=LoadBalancer --port=80 --target-port=5000

```