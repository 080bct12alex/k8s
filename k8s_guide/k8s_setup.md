# Kubernetes Local Setup:
 *Minikube & kubectl local setup*
 
 - [ Minikube Installation Guides](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)   ---> prefer for learning  as it provides kubectl as well 

- [kubectl Installation Guides](https://kubernetes.io/docs/tasks/tools/) 


*Local Kubernetes Setup:*


![Local Kubernetes Setup](https://i.ibb.co/VYqYJmRL/Kubectl-Interaction-with-Cluster.png)

- **Minikube**:      
  - Lightweight Kubernetes implementation that creates a Single-node local cluster (runs master+worker)  Kubernetes cluster on your machine.
  - The node created is a virtual machine or container
  - This is a complete Kubernetes environment but is designed for local development, not for production workloads.
  - In a typical Kubernetes setup, the control plane (master) and worker nodes are separate, but for local development, Minikube combines these into one node.
  - Lightweight Kubernetes implementation that creates a local Kubernetes cluster on your machine.
  - Ideal for development and testing purposes.
  - with a pre-installed Docker container runtime.
  - Needs Docker/VM as driver.
  -  Docker being the preferred driver across operating systems
  -  If we use minikube with Docker driver, it uses the existing Docker installation on the host machine to run the Minikube VM as a container.
  - Install via brew (Mac) or package manager (Linux/Windows).

- **kubectl**: 
  - CLI tool 
   - command-line tool used to interact with any Kubernetes cluster, including Minikube, by sending commands to the API server.
   - kubectl is typically installed as a dependency when Minikube is installed.
      - `kubectl get all`
      - `kubectl describe pod <pod-name>`
      - `kubectl logs <pod-name>`
      - `kubectl apply -f <file>.yaml`
      - `kubectl delete -f <file>.yaml`

***Quick commands:***

 *- Install minikube & kubectl (platform-specific).*

- The installation process involves installing Docker Desktop (if not already present) 

*- Start minikube (use Docker driver (recommended) / or others):*

```bash
  minikube start --driver=docker
```

- When you run minikube start for the first time, it will create a local single-node Kubernetes cluster , which acts as both the master node (for controlling the cluster) and the worker node (for running workloads) .
kubectl is automatically configured to interact with this local cluster using the kubeconfig file located at `~/.kube/config`.


*Note:*
-  You can switch between multiple clusters (local or  remote ) using 
```bash
kubectl config use-context
```
```bash
kubectl config current-context
kubectl config get-contexts   # List all contexts
kubectl config use-context <new-context-name>   # Switch context
```

```bash
docker --version
docker ps

minikube start --driver=docker
minikube --help
docker ps
minikube status
minikube dashboard

kubectl version
kubectl get nodes
kubectl get pods -A
kubectl logs <podname>
kubectl describe pods

kubectl get services -A
kubectl get all -A
kubectl cluster-info
kubectl config current-context
kubectl config get-contexts   # List all contexts
kubectl config use-context <new-context-name>   # Switch context
kubectl get services -A


minikube ip
minikube service list
minikube profile list # list all profiles/clusters
minikube profile <profile-name> # switch to a different profile
minikube start -p <profile-name>  # start a new profile/cluster
minikube stop      # stop the default profile
minikube stop -p <profile-name>
minikube delete   # delete the default profile
minikube delete -p <profile-name>


# Change the default memory limit (requires a restart):
minikube config set memory 9001

# Browse the catalog of easily installed Kubernetes services:
minikube addons list

```
