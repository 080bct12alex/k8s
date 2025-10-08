# AWS EKS
- Elastic Kubernetes Service (EKS) is a managed Kubernetes cluster service provided by AWS. 
- AWS manages the master nodes (control plane) for you, handling their creation, installation of necessary applications (like container runtime and Kubernetes master processes), scaling, and backups. 
- This management allows small teams to focus on deploying applications in Kubernetes without worrying about master node maintenance. 
- Users are primarily responsible for creating and managing only the worker nodes. 


AWS EKS Overview



---
Manual AWS EKS Cluster Creation Process
-
Creating an EKS cluster manually
 involves several steps and configurations:

1. ***Preparation***
   - Create an AWS account. 
   - Set up or use an existing Virtual Private Cloud (VPC), which is your isolated private space within AWS. 
   - Create an IAM role with a security group, granting permissions to interact with the EKS service. 


2. *Control Plane Creation:*

- Create the cluster itself, specifying basic information like the cluster name, desired Kubernetes version, and AWS region. 

- This step uses the previously configured IAM role. 


   > Cluster creation can be done via the AWS Management Console (UI) or the AWS Command Line Interface (CLI). 



3. ***Worker Node Setup:***

- After the control plane is ready, create worker nodes and connect them to the cluster. 
- ***Worker nodes are EC2 instances*** provisioned as a node group, not individual instances.
- When creating a node group, specify the cluster to join, security group, and EC2 instance type (CPU, RAM, storage). 
- Node groups support auto-scaling, allowing automatic addition or removal of worker nodes based on cluster load, by defining maximum and minimum node counts. 

4. **Local Connection:**

- Finally, connect to the remote cluster from your local machine using the kubectl (Kubernetes command-line tool) to deploy applications. 


Manual EKS Cluster Creation






**Challenges with Manual EKS Setup**

- The manual process requires significant effort and a deep understanding of multiple AWS services. 
- It can be complex for users new to AWS or those who prefer a quicker setup. 
- While powerful, AWS EKS setup is considered relatively complex compared to other managed Kubernetes services like Linode Kubernetes Engine. 



---

## Simplified EKS Cluster Creation with eksctl

- eksctl is a community-driven command-line tool developed by Weaveworks, designed to simplify EKS cluster creation. 
- It streamlines the process by performing all necessary background configurations (VPC, subnets, IAM roles, etc.) with a single command. 
- eksctl uses default values but allows customization through command-line parameters for specific cluster names, Kubernetes versions, or node types. 


**eksctl Installation and Usage**

*Installation:*

```bash
winget install Weaveworks.eksctl

eksctl version
```
- This also installs dependencies like kubectl and aws-iam-authenticator. 


***Prerequisite:***

Before using eksctl, ensure your AWS user credentials are configured locally for authentication. 

**Creating a Cluster:** 
- The eksctl create cluster command is used, allowing various parameters to override defaults:

```bash
eksctl create cluster \
  --name=test-cluster \
  --version=1.23 \
  --region=eu-central-1 \
  --nodegroup-name=linux-nodes \
  --node-type=t2.micro \
  --nodes=2
  ```

- This single command handles the creation of the cluster, VPC, subnets, and the specified node group with EC2 instances. 


**Connecting to the Cluster:** 
- eksctl automatically generates a kubeconfig file with cluster details (endpoint, certificate) to enable kubectl on your local machine to connect. 

```bash
kubectl get nodes
```

- This command will list the connected worker nodes. 

**Deleting a Cluster:** 
- eksctl also simplifies cleanup, removing all associated resources (EC2 instances, node groups, etc.) with a single command:

```bash
eksctl delete cluster --name=test-cluster
```
- This process automates the termination of all cluster resources. 





