# Kubernetes Basics
 **Kubernetes Overview**
- Kubernetes is a container orchestration platform 
- that manages containerized applications across a cluster of machines. 
- It automates deployment, scaling, and operations of application containers. 

**Key Components of Kubernetes**
- API Server: 
  - Provides a RESTful interface for interacting with the Kubernetes cluster. 

- Scheduler: 
  - Assigns nodes to newly created pods based on resource availability.

- Controller Manager: 
  - Manages the state of the cluster, ensuring that the desired state matches the actual state. 

- Kubelet: 
 - An agent that runs on each node, ensuring that containers are running in pods as expected. 

- Kube Proxy: 
  - Maintains network rules for communication between pods and external networks. 

***Key Concepts***

- Pod:
  
  - The smallest deployable unit in Kubernetes, which can contain one or more containers. 
 
  - Each pod runs in an isolated environment and shares resources. 
  
  - Each Pod shares the same network namespace and can communicate with each other using localhost.

- Container:
   
   -  A running instance of an image, which is a lightweight, standalone, executable package that includes everything needed to run a piece of software. 



***Creating a Pod*** 
- Pods  can create a pod  using a YAML file or command line.
- To create a pod,  first need an image of the application or use pre-defined applications from Docker Hub image. 

*Using a YAML file*
- Using yml file, its easy for deployment , services
  also for update, rollout ,roll back ; only need to chmage version of image in the deployment file 
-  only need to use 
```bash
kubectl apply -f <filename>.yaml
```
- For Deployment:
```bash
kubectl apply -f my-app-deployment.yaml
```

***Checking Deployment Status*** 

- check to see the state of the pods created by the deployment. 
```bash
kubectl get deployments
``` 
```bash
kubectl get pods
```

*Dashboard and Monitoring*

- Kubernetes provides a dashboard  to monitor the status of  deployments, pods, and services.

- start the dashboard using

```bash
 minikube dashboard
 ```

 **Deleting**

```bash
kubectl delete  <file-name.yml>
```

***Accessing Applications***
- By default, applications listen on port 80.
- By default, Applications running in Pods are not accessible from outside the cluster. 
using: localhost:80
- **To access** the application running in a pod,  *expose it using a* **service** object. like in docker port binding
- using service yaml file.

Then, 
*tell the minikube about the service created*
```bash
minikube service <service-name> 
```

*Services*
- To check the services created, use 

```bash
kubectl get services
```

**Deleting**

```bash
kubectl delete  <service-name>
```



*get started way(normal way)*
- lets start using pre-defined applications from Docker Hub image, such as  Nginx. 
- here using single cointainer in single pod
- Use the command to create a deployment, which automatically creates a pod.

```bash
kubectl create deployment <deployment-name> --image=<image-name> 
```
- Example:
```bash
kubectl create deployment my-app --image=nginx
```
- simple way of creating deployment for testing  without yml file
  but lag in others features mentioned n above yml case. we need to use command more in here.

***Checking Deployment Status(for yml or normal)*** 

- check to see the state of the pods created by the deployment. 
```bash
kubectl get deployments
``` 
```bash
kubectl get pods
```

*Dashboard and Monitoring*

- Kubernetes provides a dashboard  to monitor the status of  deployments, pods, and services.

- start the dashboard using

```bash
 minikube dashboard
 ```

***Accessing Applications***
- By default, applications listen on port 80.
- By default, Applications running in Pods are not accessible from outside the cluster. 
using: localhost:80
- **To access** the application running in a pod,  *expose it using a* **service** object. like in docker port binding
   
   - Use the command to create a service that exposes the application.
 
 ```bash
kubectl expose deployment <deployment-name> --port=<port> --type=LoadBalancer 
```
- example:
 ```bash
kubectl expose deployment my-app --port=80 --type=LoadBalancer 

```

Then, 
*tell the minikube about the service created*
```bash
minikube service <deployment-name> 
```
- example:


- This will show you the external IP and port like http://127.0.0.1:54074 through which you can access your application. 


***Managing Services***
- To check the services created, use 

```bash
kubectl get services
```
 

**Deleting**
```bash
kubectl delete deployment <deployment-name>
```


***Scaling  Applications***
- Kubernetes allows to scale applications easily 
- increase the number of replicas in a deployment:

```bash
kubectl scale deployment my-app --replicas=3
```



***Updating Applications (Rollout Update)***
- When you need to update your application to a new version or old version ,  
- For new version
   - create a new image and push it to a Docker registry. use:


```bash
docker build -t <image-name>:<version> . 
```

```bash
docker push <image-name>:<version> 
```

- After pushing the new image, update the deployment with the new image using

```bash
 kubectl set image deployment <deployment-name> <container-name>=<new or old-image-name>:<version> 
 ```

 - In this way we can switch to any version of our app

- This will update the deployment with the new configuration. 



*Monitor the Rollout:(same for yml)*

```bash
kubectl rollout status deployment my-app
```

*Roll Back :*
- If something goes wrong, you can roll back to the previous version with:

```bash
kubectl rollout undo deployment my-app
```


***Handling Failures***
- self healing
- Kubernetes automatically manages the lifecycle of Pods. If a Pod crashes, Kubernetes will restart it to maintain the desired state. This ensures high availability of applications. 

- If an application crashes (delete,update), Kubernetes automatically restarts the container to ensure the application remains available. 

- However, if the data is stored within the container, it may be lost upon restart. 

*using yml file*
***Data Management***
- To manage data effectively, especially for databases like MongoDB, it is crucial to store data outside of containers  most better pod to prevent data loss during container restarts.
- use external databases or persistent storage solutions to ensure data durability.
- Use persistent storage solutions provided by Kubernetes to ensure data durability if using self-hosted databases like MongoDB i.e using mongodb image from docker Hub  which is like using localhost MongoDB. 
- For production environments, consider using managed database services or external databases like MongoDB Atlas to ensure data persistence and reliability.
- Note: we can use storage inside pod or container for application that need storing data temporarily like cache, session data etc.  
But for databases and critical data, always use external databases or persistent storage solutions

***Persistent Storage***
- If use Self-hosted MongoDB (in Kubernetes) ,  need to ensure that the data is stored outside of pod to prevent data loss during container restarts.
- To manage data persistence, use volumes to store data outside of the container lifecycle, preventing data loss during container restarts. 
- Kubernetes provides Persistent Volumes (pv) and  Persistent Volumes Claims (pvc) for Persistent Storage.

![Local Kubernetes Persistent Storage Setup](https://k21academy.com/wp-content/uploads/2020/11/cs_storage_pvc_pv-298x300.png)



- Also Kubernetes provides various types of volumes, such as hostPath, NFS, and cloud provider-specific volumes (e.g., AWS EBS, GCE Persistent Disk).
- Volumes allow data to persist beyond the lifecycle of individual Pods. You can define a volume in your Pod specification to store data.  


***Conclusion***

- Kubernetes is a powerful tool that provides a robust framework for managing containerized applications, providing features for deployment, scaling, monitoring, and data management . . 

- Understanding its core concepts ,components and commands is essential for effective management of applications in a Kubernetes environment. 



