**Kubernetes**
- Kubernetes is a generic platform that is not dependent on any specific cloud technology. 
- It can run anywhere, including AWS, DigitalOcean, GCP, or on bare metal servers. 

      
      Kubernetes is a generic, cloud-agnostic technology that ensures applications run consistently across various environments, whether on AWS, DigitalOcean, GCP, or bare metal servers/local machine. 


***Kubernetes Architecture***
- Here is a mind map illustrating the core components of Kubernetes architecture:

![Kubernetes Architecture](https://i.ibb.co/v6rmfGzW/Kubernetes-Architecture-Components.png)


A Kubernetes cluster is fundamentally composed of a Control Plane and Worker Nodes, all running on physical machines. 

- Control Plane 
  - The Control Plane is a physical machine where key Kubernetes components are installed. 

  - The Control Plane is responsible for managing and orchestrating the cluster, and it includes several key components:-


    - API Server: 
      - The central hub that exposes the Kubernetes API, allowing communication with the cluster. 
      - Provides a RESTful interface for interacting with the Kubernetes cluster.

    - Scheduler: 
      - Assigns nodes to newly created pods based on resource availability.
  
    - Kube-controller: 
       - Manages various controllers that regulate the state of the cluster. 

    - Controller Manager: 
      - Manages the state of the cluster, ensuring that the desired state matches the actual state. 
    
    - etcd: 
        - A key-value storage that holds the current state of the Kubernetes cluster (configuration and status data).  
        
        - Snapshots of etcd are used for cluster recovery/backup.


- Worker Nodes 
   - The actual containers and application workloads run within the Container Runtime Interface (CRI) on these worker nodes. 
   - physical machine that has Kube-proxy, Kubelet, and a Container Runtime Interface (CRI) installed.
 
     - Kubelet: 
        - An agent that runs on each node in the cluster, ensuring that containers are running in a Pod as expected.  

        - Kubelet on the worker nodes connects to the API server in the Control Plane. 
   
   - Kube-proxy: 
     - Maintains network rules on nodes, enabling network communication to Pods from inside or outside the cluster.
     - Maintains network rules for communication between pods and external networks. 
     
   - Container Runtime Interface (CRI):  
      -  The software responsible for running containers. Examples include containerd, Docker Engine, Podman, and CRI-O. 

       - Containerd is specifically mentioned as a runtime that enables containers to run within Kubernetes. 
    
    - Pods → smallest deployable unit, run one or more containers

