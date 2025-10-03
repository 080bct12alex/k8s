# ( history → architecture)
  
## 1. Introduction  

Kubernetes (K8s) :
- container orchestration system.
-  solves modern deployment challenges:
    - Scaling applications as traffic changes.
    - Deployment automation across environments.
    - Managing containers (starting, stopping, monitoring, recovery). 

## 2. History of Deployment 
- from traditional servers to Kubernetes architecture.

*a. Physical Servers (Bare Metal)*

- Developers wrote code (e.g., JavaScript, PHP).
 
- To deploy → needed a physical  deployment server:

   - 24/7 availability.

   - internet access.

  - Static IP for users to reach it.

- **Challenges:**

   - Manually installing dependencies (Redis, Postgres).

   - Must replicate local environment on server.

   - “Works on my machine” problem.

   - Scaling = upgrading CPUs/RAM → costly and hard


*b. Cloud & AWS*

- AWS changed deployment → introduced on-demand servers (no upfront purchase).

*Benefits:*

- Pay only for what you use.

- No upfront cost.

- Anyone (student, startup, company) can deploy apps quickly.

- AWS provided cloud-native services:

   - Managed databases.

   - Load balancers.

   - Auto-scaling groups.

*Problem:*

- Easier than bare metal, but environment mismatch still existed.


- Still need to replicate environment.

- **Vendor lock-in**: AWS ECS configs don’t work on DigitalOceanor GCP.

*c. Virtual Machines (VMs)*

-  Virtualization.

- VM images contain **entire OS** + app + dependencies.

Issues:

- Large size (4–10GB) as include full OS. 

- Slow startup.

- Hard to scale quickly.

- Heavy to share.

*d. Containers*
- ***lightweight virtualization using host OS kernel.***

Features:

- No need to include full OS.

- **Portable images → same behavior everywhere.**

- Small, fast, and easy to share.

- Solved “works on my machine” issue.

- Enabled fast deployment , rapid scaling → basis for modern deployment.

*3. Container Orchestration*

- Containers solved environment mismatch but introduced management challenges:

   - Start/stop containers.

   - Scale containers up/down based on traffic.

   - Restart failed containers.

   - Collect logs.

   - Monitor health.

- Doing this manually is not practical / scalable.

- This led to the concept of Container Orchestration = automation of container management.

4. From Google Borg to Kubernetes

- Google built Borg, an internal system to manage containers.

- Later, they built Kubernetes from scratch (not Borg code).
   - Kubernetes was built from scratch, inspired by Borg (not a direct open-source release).

- Donated to CNCF (Cloud Native Computing Foundation) in 2014.

- Kubernetes = open-source, cloud-agnostic orchestration system → widely adopted.


*5. Kubernetes Basics*

-  Kubernetes = Greek word for ship pilot.
   - thus logo as ship stiring

-  K8s (K + 8 letters + s).

*Definition:*

- Automates deployment, scaling, and management of containerized apps.

- Groups containers into logical units.

- Key feature: Cloud-agnostic → runs on AWS, GCP, DigitalOcean, bare metal servers, etc.

*6. Kubernetes Architecture*

a. Control Plane
- the “brain”
- Manages and controls the entire cluster.

Components:
- API Server → receives user instructions (via kubectl).
  - Entry point for all instructions (via kubectl).
  - Exposes APIs for communication.

- Controller Manager
  - Ensures the cluster state
   - matches the desired state.

- Scheduler → Assigns pods to worker nodes based on resources.

- etcd → Distributed key-value store for cluster state  and configuration.



*b. Worker Nodes*

- the “muscle”
- Physical/virtual machines where applications actually run.

Components:

- Kubelet 
   - agent running on each node that communicates with control plane.
   - Ensures containers are running as instructed.

- Kube-proxy 
     - Manages networking and load balancing.

- Pods → smallest deployable unit, run one or more containers




*7. Core Components*

- Pods → abstraction over containers.

- Services 
     -  expose pods inside/outside cluster.
     - expose pods to internal/external traffic.

- Ingress → manages external access (HTTP/HTTPS).

- ConfigMaps → store non-sensitive configuration data.

Secrets → store sensitive data (e.g., passwords, API keys).

Volumes → provide persistent storage for pods


*8.  Tools*

- Docker → makes containerization easy.
   - popular tool for creating container images.

- AWS ECS → cloud-specific orchestrator (example of vendor lock-in). 

- Kubernetes → cloud-agnostic alternative to ECS.

- kubectl → CLI tool to interact with Kubernetes API.

- Minikube → lightweight tool to run Kubernetes locally


*9. Why Kubernetes is Popular*

- Cloud-agnostic → no vendor lock-in.
- Automates container orchestration→ no manual scaling/monitoring.
- Highly scalable .
- Reliable & fault-tolerant → self-healing (restarts failed pods).


*10. How to use*

- Kubernetes uses YAML manifests to define desired state:
   - metadata (name, labels).

   - spec (what to run, replicas, image).

   - status (current running state).

*Example : MongoDB Deployment on Kubernetes*

Step 1: Write a Deployment YAML (MongoDB container, replica count).

Step 2: Apply with **kubectl apply -f**.

Step 3: Expose with a **Service**.

Step 4: Scale replicas using **kubectl scale**.


✅ Summary


- Deployment history → physical → cloud → VMs → containers.

- Orchestration need → Borg → Kubernetes.
- Why Kubernetes is popular (scalable, reliable, cloud-agnostic).
- Kubernetes basics.

- Architecture (control plane, worker nodes).

- Core components (pods, services, etc.).

- Tools (Docker, Minikube, kubectl).

- How to use

- YAML configs & MongoDB deployment







**Conclusion**

 *Traditional Application Deployment Challenges*

 ![Traditional Application Deployment](https://i.ibb.co/Lh5njKtC/Traditional-Application-Deployment.png)
- Deploying applications traditionally involved manually installing software like Redis version 6 or PostgreSQL version 14 on a physical machine. 

- The application code then needed to be cloned onto this machine, often via FTP, direct copy, or from a version control system like GitHub. 

- A common issue was the "it works on my machine" problem, where code might not run identically in the production environment as it did locally. 

- Once the code was running, a domain (e.g., example.com) would be purchased and routed to a static IP address to make the application accessible. 

- This  Traditional deployments face issues like downtime if the application crashes and lack of scalability for increasing traffic, as they are static.

- To handle increased traffic, developers traditionally had to upgrade hardware by purchasing and installing new CPUs and RAM (e.g., from 2 CPUs/4GB RAM to 4 CPUs/8GB RAM, or 8 CPUs/64GB RAM). This approach was a constant, painful, and costly process requiring skilled personnel and was not easily scalable.


# Evolution with Cloud Computing
- The introduction of cloud providers like AWS significantly changed deployment by making cloud infrastructure generally and easily available to everyone. 
- dependent on any specific cloud technology


**Understanding Kubernetes**
- Kubernetes is a generic platform that is not dependent on any specific cloud technology. 
- It can run anywhere, including AWS, DigitalOcean, GCP, or on bare metal servers. 

      
      Kubernetes is a generic, cloud-agnostic technology that ensures applications run consistently across various environments, whether on AWS, DigitalOcean, GCP, or bare metal servers/local machine. 


