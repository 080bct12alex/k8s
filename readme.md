# Kubernetes 
 This repo includes a comprehensive guide to understanding Kubernetes (K8s), containerization (Docker), and deployment management.

## Topics covered:

- [ Kubernetes](#introduction-to-kubernetes)
- [Container Basics](#container-basics)
- [Kubernetes Architecture](#kubernetes-architecture)
- [Deployments & Pods](#deployments--pods)
- [Scaling & Load Balancing](#scaling--load-balancing)
- [Service Discovery](#service-discovery)


- [Project](#webapp-simple-project)
  - [Deployment of NGINX](#deployment-of-nginx)
  - [Deployment of Webapp](#deployment-of-webapp)
  - [Service Exposure](#exposing-port-using-service)
  - [Rollout & Rollback](#rollout-in-k8)
  - [Self-Healing](#self-healing-in-k8)
  - [Scaling the App](#scaling-our-app)
  - [YAML Config Files](#yaml-config-for-deployment-and-service)

  - [Multi-container Apps](#overview-of-multi-container-app)
  - [ConfigMaps](#configmaps)
  - [Volumes and Persistent Storage](#volume-and-data)
- [Conclusion](#conclusion)

##  Kubernetes

Kubernetes (K8s) is an open-source container orchestration platform for automating the deployment, scaling, and management of containerized applications.

## Container Basics

Containers are lightweight, portable, and self-contained environments where applications can run consistently across different environments. Kubernetes manages the deployment, scaling, and networking of these containers. 

## Kubernetes Architecture

Kubernetes is built around a master-slave architecture, consisting of the **Master Node** (responsible for managing the cluster) and **Worker Nodes** (where the application containers run). The major components include:
- API Server
- Scheduler
- Controller Manager
- etcd (a key-value store)
- Kubelet (node agent)
- Kube Proxy

## Deployments & Pods

A **Pod** is the smallest deployable unit in Kubernetes and can contain one or more containers. A **Deployment** ensures that a specified number of Pods are running at all times, providing a robust way to manage application lifecycle.

## Scaling & Load Balancing

Kubernetes provides automatic scaling of applications, both horizontally (adding more instances) and vertically (increasing resources). It also balances traffic using Services, ensuring requests are routed to healthy Pods.

## Service Discovery

In Kubernetes, Services act as an abstract layer that allows Pods to communicate with each other, even if they are distributed across multiple nodes in the cluster.
---

##  Demo
 - Deploying a simple web application using Kubernetes.

### Deployment of NGINX
 Deploying using image of  NGINX  from Docker Hub, a popular web server, on Kubernetes.

### Deployment of Webapp
Deploy  simple web application on Kubernetes.

### Exposing Port Using Service
Expose app using Kubernetes **Services** to allow traffic from outside the cluster.

### Rollout in K8
Understand how to manage rolling updates with Kubernetes Deployments.

### Rollback in K8
Learn how to roll back deployments to a previous state when something goes wrong.

### Self-Healing in K8
Kubernetes automatically restarts failed Pods, ensuring high availability for  apps.

### Scaling Our App
scale  app horizontally to handle increased traffic.

### YAML Config for Deployment and Service
- Kubernetes YAML files that define Pods, Deployments, and Services.

---

###  Multi-container App
Learn how to deploy multi-container applications within a single Pod.

### ConfigMaps
Using ConfigMaps to manage configuration data separately from application code. 

### Volume and Data
Managing persistent data with **Volumes** and **Persistent Volumes** in Kubernetes.

---

## Conclusion

- learned the fundamentals of Kubernetes, from basic container concepts to advanced deployment strategies. 

---

## Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Docker Documentation](https://docs.docker.com/)
- [Minikube Setup](https://minikube.sigs.k8s.io/docs/)
