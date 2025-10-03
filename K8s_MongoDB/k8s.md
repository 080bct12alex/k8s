# Kubernetes: step-by-step   theory → setup  → YAML → commands → project → troubleshooting.


**1 .  Kubernetes  (k8s)**

-------------------------------------------------------------------
1. INTRODUCTION
-------------------------------------------------------------------

-  an open-source container orchestration system / framework .

- originally developed by Google.

- Purpose: Manage containerized applications (Docker or other runtimes) at scale across physical, virtual, cloud, or hybrid environments.


***Problem it solves:***
  - trend from monolith to microservices.
  - Microservices created huge numbers of containers.
  - Managing hundreds/thousands of containers manually is complex.
  - Kubernetes solves problems of availability, scalability, and recovery.


***Key capabilities:***
- **High Availability** → ensures no downtime, apps always accessible.
- **Scalability** → scale apps up or down depending on demand.
- **Disaster Recovery** → restore cluster from backups (etcd snapshots).

- Self-healing (restart/recreate containers that fail).

- Declarative desired state.

- Service discovery & load balancing.

- Config/secret management, persistent storage, rolling updates, resource management.



-------------------------------------------------------------------
2. ARCHITECTURE OVERVIEW
-------------------------------------------------------------------

![Kubernetes Architecture](https://i.ibb.co/v6rmfGzW/Kubernetes-Architecture-Components.png)
- A Kubernetes cluster consists of at least one master node and multiple worker nodes. 
- A Kubernetes **Cluster** = Master Node(s) + Worker Nodes.


*components:*

- Control plane (master node(s))
 
    → Runs critical Kubernetes processes
    
    → Needs backup for production (multiple master nodes recommended).

  - API Server :
      - single  Entry point (CLI, API, Dashboard connect here) for  all REST operations. 
     - kubectl talks here.
     - kube-apiserver


   - Controller Manager :
      - Ensures desired state (e.g., restarts dead pods). 
     - runs controllers (replication controller, node controller, endpoints controller...). 
     - kube-controller-manager


    - Scheduler (kube-scheduler):      
      - Places pods intelligently on nodes based on resources.
      - assigns new pods to nodes based on resource requirements
      - kube-scheduler
  

   - etcd: 
      - distributed key-value store holding cluster state (config, metadata). Back this up regularly.

   - Cloud controller manager (when in cloud):
     - integrates cloud provider APIs.

- Worker nodes
  - Runs actual applications (pods/containers).

  - More powerful (CPU/RAM) because it runs the workload.

    - kubelet:

        - agent to communicate with master
        - agent on each node; ensures containers described in PodSpecs are running.

    - kube-proxy: 
        - implements Service networking (cluster IPs, load balancing) on each node.

    - Container runtime:
      - Docker, containerd, CRI-O, etc.



***Cluster network***
- Each Pod gets its own IP.

- Network plugin (CNI): Flannel, Calico, Weave, Cilium — provides pod-to-pod connectivity and policy.

- Virtual Network across nodes → pods can communicate as if one machine.


-------------------------------------------------------------------
3. CORE COMPONENTS
-------------------------------------------------------------------


![Kubernetes CORE COMPONENTS](https://i.ibb.co/SwjjgSRt/Core-Kubernetes-Components.png)

- Pod
  - Smallest deployable unit; one or more tightly coupled containers sharing network namespace and volumes.
  - Smallest unit, abstraction over containers.
  - Usually 1 container per pod .
  - Each pod gets an IP.
  - Pods are ephemeral, meaning they can die and be replaced, receiving a new IP address upon recreation. 
  - Ephemeral:
 
     -  replaced often. Don’t store data inside pods unless backed by volumes.


- Service
  - Provides stable / static IP and DNS name for a pod.
  - ensuring consistent communication even if pods restart, and also functions as a load balancer.
  - Handles pod restarts (IP changes).
  - Can be:
     - **ClusterIP** (default, internal)
     - **NodePort** (external access)  → exposes a port on every node
     - **LoadBalancer** (cloud provider integration).  → cloud provider creates LB
     - **ExternalName** (DNS alias)

  - Stable network endpoint (ClusterIP) in front of a set of pods (selected by labels).

   
- Ingress
   - route external traffic to services using secure protocols and domain names
   - Provides domain-based routing, HTTPS, neice URLs.
   - High-level HTTP/S routing rules (host/path → Service). Requires an Ingress Controller (nginx-ingress, Traefik, etc.).
   - Use for host-based routing, TLS termination.

- ConfigMap
  - Key/value configuration (non-sensitive). 
  - Inject as env vars or files.
  - External configuration (e.g., DB URL).
  - allowing changes without rebuilding application images.
- Secret
    - Stores sensitive data (passwords, certs).
    - Encoded in base64 (should be encrypted).

- Volume / PersistentVolumeClaim (PV/PVC)
   - Attaches external storage to pods.
   - Persistent across pod restarts.
   - Local or remote/cloud storage.
  - PV: 
     - cluster resource representing physical storage (NFS, cloud disk).

  - PVC: 
     - request by a Pod for storage; binds to a PV.

  - Use PVC for databases or any stateful services.
  - Volumes attach physical storage to pods, ensuring data persistence even if the pod restarts; this storage can be local or remote, and Kubernetes itself does not manage data persistence.

-------------------------------------------------------------------
4. HIGH AVAILABILITY & REPLICATION 
--------------------------------------------------------------

- Replication & scaling (Deployment vs StatefulSet)



***Deployment***
- Blueprint for pods.
- used to define and manage the desired number of pod replicas, enabling scaling up or down for stateless applications.
- Specifies number of replicas, scaling 
- Manages ReplicaSets → pods. 
- For stateless apps.
- Provides self-healing: ensures desired = actual state.
- Rolling updates and rollbacks built in.

- Example: web frontends, microservices.

-------------------



**StatefulSet**
- Used for stateful apps (databases).
- Ensures data consistency with shared storage.
- For stateful apps that need stable network id, stable storage (per-replica PVCs), ordered start/stop.
- StatefulSet gives stable pod names like mongo-0, mongo-1.

- Example: databases (Cassandra, Mongo replica sets when running in-cluster), Zookeeper.


-------------------

-------------------------------------------------------------------
5. CONFIGURATION (YAML/JSON)
-------------------------------------------------------------------
-------------------

- Every config has 3 sections:
  1. Metadata (name, labels)
  2. Spec (desired configuration)
  3. Status (auto-added by Kubernetes)

- YAML is strict on indentation.
- Config files should be stored with app code .

- Every resource YAML has:
   - apiVersion

   - kind

   - metadata (name, namespace, labels)
        - Labels 
           - (key-value pairs) used to identify and address specific components, and selectors (e.g., in Deployments and Services) use these labels to match and manage related pods.
   - spec (desired configuration)

   - status (filled by k8s; do not write)
       -  Automatically generated and updated by Kubernetes, reflecting the actual state of the component. 
   Kubernetes provides a self-healing feature by continuously comparing the desired state (from the specification) with the actual state (from etcd), and if they don't match, Kubernetes automatically takes action to fix it (e.g., restarting a crashed pod).
   
   - Environment variables can be configured within container definitions to pass data from ConfigMaps or Secrets to applications.. 
   
   


- Example (Deployment ):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myorg/myapp:1.0
        ports:
        - containerPort: 80
```







-------------------


## 6. Minikube & kubectl setup (local testing)


*Local Kubernetes Setup:*

- Here is a flowchart that visualizes the interaction:

![Local Kubernetes Setup](https://i.ibb.co/VYqYJmRL/Kubectl-Interaction-with-Cluster.png)

- **Minikube**:      

  - Single-node local cluster for testing (runs master+worker).
  - with a pre-installed Docker container runtime.
  - Needs Docker/VM as driver.
  -  Docker being the preferred driver across operating systems
  - Minikube itself runs as a Docker container, and it also includes Docker to run application containers inside it, creating two layers of Docker.
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

*- Start minikube (use Docker driver / or others):*

```bash
  minikube start --driver=docker
```

*- See nodes:*

```bash
kubectl get nodes
```

*- Optional: enable ingress (for Ingress demos)*
minikube addons enable ingress


*- To get cluster IP (minikube VM IP):*

```bash
minikube ip
```

*- Stop/delete cluster:*
```bash
minikube stop
```
```bash
minikube delete
```

-------------------


## 7. MongoDB + Web app
- with ConfigMap & Secret.


- Files:
1. **config.yaml** → ConfigMap with MongoDB URL.
2. **secret.yaml** → Secret with base64 encoded username/password.
3. **mongo.yaml** → Deployment + Service for MongoDB.
4. **webapp.yaml** → Deployment + Service (NodePort) for WebApp.


***A — ConfigMap (configmap.yaml)***

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-config
data:
  DB_URL: "mongodb://mongo-service:27017/mydb"
```

-------------------


***B — Secret (secret.yaml)***

Create base64 encoded values first (or use kubectl create secret).

```bash
# Generate base64 (recommended use -n to avoid newline)
echo -n "demouser" | base64   
echo -n "demopassword" | base64
```

Then secret YAML:

```yml
apiVersion: v1
kind: Secret
metadata:
  name: demo-secret
type: Opaque
data:
  MONGO_INITDB_ROOT_USERNAME: ZGVtb3VzZXI   # base64 of demouser
  MONGO_INITDB_ROOT_PASSWORD: ZGVtb3Bhc3N3b3Jk  # base64 of demopassword
```

Alternative: simpler:

```bash
 kubectl create secret generic demo-secret --from-literal=MONGO_INITDB_ROOT_USERNAME=demouser --from-literal=MONGO_INITDB_ROOT_PASSWORD=demopassword
```
-------------------


***C — MongoDB Deployment + Service (mongo-deployment.yaml)***

(This uses a single replica and a PVC for persistence .)


```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
  labels:
    app: mongo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
      - name: mongo
        image: mongo:5.0
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: demo-secret
              key: MONGO_INITDB_ROOT_USERNAME
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: demo-secret
              key: MONGO_INITDB_ROOT_PASSWORD
        volumeMounts:
        - name: mongo-data
          mountPath: /data/db
      volumes:
      - name: mongo-data
        persistentVolumeClaim:
          claimName: mongo-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongo
  ports:
  - name: mongodb
    port: 27017
    targetPort: 27017
  type: ClusterIP
```

- we can add different yaml  in single file by separating ymal by ---

**Accessing WebApp:**
- Get Minikube IP: `minikube ip`
- Access in browser: `http://<minikube-ip>:<node-port>`

-------------------


***D — Persistent Volume Claim (simple hostPath for minikube) (mongo-pvc.yaml)***

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

On minikube this will bind to the default StorageClass which uses hostPath via dynamic provisioner.

***E — Web application Deployment + Service (webapp-deployment.yaml)***

-  webapp image connects via env: DB_URL, MONGO_USER, MONGO_PASS.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-webapp
  labels:
    app: demo-webapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo-webapp
  template:
    metadata:
      labels:
        app: demo-webapp
    spec:
      containers:
      - name: webapp
        image: yourdockerhubuser/demo-webapp:v1   # replace with real image
        ports:
        - containerPort: 3000
        env:
        - name: DB_URL
          valueFrom:
            configMapKeyRef:
              name: demo-config
              key: DB_URL
        - name: MONGO_USER
          valueFrom:
            secretKeyRef:
              name: demo-secret
              key: MONGO_INITDB_ROOT_USERNAME
        - name: MONGO_PASS
          valueFrom:
            secretKeyRef:
              name: demo-secret
              key: MONGO_INITDB_ROOT_PASSWORD
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    app: demo-webapp
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 30100
```


***F — Apply resources in order***

- Components are created using the kubectl apply -f [filename] command

- Order matters because webapp & mongo may reference config/secret/PVC:

```bash
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
kubectl apply -f mongo-pvc.yaml
kubectl apply -f mongo-deployment.yaml
kubectl apply -f webapp-deployment.yaml
```




***G — Validate & access***

```bash
kubectl get all
kubectl get pods -o wide
kubectl get pvc
kubectl describe pod <pod-name>
kubectl logs deployment/demo-webapp      # logs for webapp
kubectl logs pod <mongo-pod-name>
# Get minikube ip and access NodePort
minikube ip         # e.g. 192.168.99.100
# In browser:
# http://<minikube-ip>:30100
```
Alternative to NodePort:

```bash
kubectl port-forward svc/webapp-service 8080:3000

# then open http://localhost:8080
```
- To make a web application accessible from a browser, its service type needs to be set to NodePort, which opens a port on the Kubernetes nodes within the range of 30000 to 32767


*notes:*

- Components are created using the kubectl apply -f [filename] command

- Common kubectl commands for interacting with the cluster include:
   - kubectl get [component_name]
      - Displays a list of components (e.g., kubectl get all, kubectl get configmap, kubectl get secret, kubectl get node). s s
    
  - kubectl describe [component_name] [instance_name]
  
     -  Provides detailed output about a specific component instance. s
  - kubectl logs [pod_name]
      -  Shows logs of containers within a pod, with a -f option for streaming. 

  - kubectl help: Provides an overview of all available sub-commands. 


***8 — Useful kubectl commands (viewing, describing, debugging)***

```bash
kubectl get nodes

kubectl get pods / kubectl get pods -o wide

kubectl get svc

kubectl get deployments

kubectl get pvc

kubectl describe pod <pod-name>

kubectl describe svc <svc-name>

kubectl logs pod/<pod-name> or kubectl logs deployment/<deploy-name>

kubectl logs -f pod/<pod-name> (stream)

kubectl exec -it <pod-name> -- /bin/sh (or /bin/bash)

kubectl apply -f <file>.yaml

kubectl delete -f <file>.yaml

kubectl rollout status deployment/<name>

kubectl rollout undo deployment/<name> (rollback)

kubectl get events --sort-by=.metadata.creationTimestamp (see recent cluster events)

kubectl top pod / kubectl top node (if metrics-server is installed)
```


***9 — Best practices & production considerations***

- Use namespaces to separate environments: kubectl create ns staging.

- Liveness & readiness probes to detect unhealthy pods and avoid sending traffic to containers not ready.

- Resource requests & limits to help scheduler and prevent noisy neighbors.

- Use Deployments for stateless, StatefulSets for stateful.

- Back up etcd and persistent data (PV snapshots).

- ImagePullPolicy, imagePullSecrets for private registries.

- Network policies for pod-level network security (Calico/Cilium).

- Enable secret encryption at rest or use external secret manager (Vault, AWS KMS).

- Use rolling updates with strategy settings to control surge and rollout speed.

- Health & metrics: integrate Prometheus + Grafana, logging (ELK / EFK) for observability.



***10 — Quick StatefulSet + Headless Service  (for DB replicas)***


```yml
apiVersion: v1
kind: Service
metadata:
  name: mongo-headless
  labels:
    app: mongo
spec:
  clusterIP: None
  selector:
    app: mongo
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongo
spec:
  serviceName: "mongo-headless"
  replicas: 3
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
      - name: mongo
        image: mongo:5.0
        ports:
        - containerPort: 27017
        volumeMounts:
        - name: mongo-data
          mountPath: /data/db
  volumeClaimTemplates:
  - metadata:
      name: mongo-data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 5Gi
```

- clusterIP: None creates a headless Service used for stable DNS names for each pod (e.g., mongo-0.mongo-headless.default.svc.cluster.local).


***11 — Troubleshooting checklist***

```bash
kubectl describe pod <pod> 
# → events show why a pod is failing (image pull, mount, crashLoopBackOff).
```

```bash
kubectl logs <pod> --previous 
# → see previous container logs if crashed.
```
Check 
```bash
kubectl get events.
```


- Validate RBAC: check ServiceAccount and Roles/RoleBindings if API calls fail.

- For networking issues, verify CNI plugin is installed and pods have IPs.

- For persistent storage: check 
```
kubectl get pvc and PV status.
```



***12 — Summary***

*- Start minikube:* 

```bash
minikube start --driver=docker
```

*- Apply resources:* 

```bash
kubectl apply -f file.yaml
```

*- See pods:* 

```bash
kubectl get pods -o wide
```

*- Pod logs:* 

```bash
kubectl logs -f pod/<name>
```

*- Exec into pod:*

```bash
 kubectl exec -it pod/<name> -- /bin/sh
```

*- Port forward:* 

```bash
kubectl port-forward svc/webapp-service 8080:3000
```
*- Delete resources:*

```bash
 kubectl delete -f file.yaml
```




-------------------------------------------------------------------
 13. KUBECTL COMMANDS (QUICK REFERENCE)
-------------------------------------------------------------------


- `kubectl get pods` → List pods
- `kubectl get svc` → List services
- `kubectl get deployments` → List deployments
- `kubectl describe pod <name>` → Detailed pod info
- `kubectl logs <pod-name>` → View logs
- `kubectl apply -f file.yaml` → Apply config
- `kubectl delete -f file.yaml` → Delete config
- `kubectl get all -o wide` → Show all components with details

-------------------------------------------------------------------
14. SUMMARY
-------------------------------------------------------------------
With just these core components:
- Pods, Services, Ingress
- ConfigMaps & Secrets
- Volumes (persistence)
- Deployments & StatefulSets

we can build production-ready Kubernetes clusters.

Minikube allows local experimentation, kubectl is the main tool for managing any cluster, and YAML configs define everything declaratively.


# conclusion
- Kubernetes provides a self-healing feature by continuously comparing the desired state (from the specification) with the actual state (from etcd), and if they don't match, Kubernetes automatically takes action to fix it (e.g., restarting a crashed pod). 

## Kubernetes Overview
- Kubernetes is an open-source container orchestration platform that simplifies managing applications made up of numerous containers across various environments. 
- Its architecture consists of master nodes that run critical processes like the API server and controller manager, and worker nodes where application pods execute. 
- Key components include services for stable IP addresses, deployments for managing application replicas, and statefulsets for stateful applications like databases, ensuring high availability, scalability, and disaster recovery.

## Kubernetes Components and Functionality
- Key Kubernetes components such as pods, services, and ingress facilitate communication and traffic routing within the cluster, 
- nwhile external configurations via config maps and secrets enhance functionality.
-  Data persistence is achieved using volumes, and pod management uses deployments and stateful sets 
- Additionally, backup and restore processes

## Creating Kubernetes Components
- Kubernetes components like pods and services are created through the API server, which acts as the sole entry point for configuration requests submitted in YAML or JSON format. 
- Configuration files consist of metadata, specifications, and status, with Kubernetes automatically managing the desired state versus actual state for self-healing. 


## Using Minikube and Kubectl
- Minikube allows users to run a single-node Kubernetes cluster on their local machine, making it easier to test applications without needing extensive resources. 
- To interact with Minikube, the command line tool kubectl is employed, which communicates with the cluster's API server. 
- Installing Minikube involves setting it up as a Docker container or virtual machine, depending on the operating system, and it conveniently includes kubectl to manage Kubernetes components.

## Deploying Applications in Kubernetes
- A local Kubernetes cluster will be used to deploy applications, specifically a MongoDB database and a web application that interacts with it via config maps and secrets. 

- Key steps include creating necessary Kubernetes configuration files for these deployments and services, implementing environment variable references to ensure secure and clean configurations, and making the web application externally accessible through NodePort services. 

- Using kubectl commands, the entire setup can be managed and monitored effectively, providing a realistic deployment scenario for users.