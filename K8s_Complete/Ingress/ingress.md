# Ingress
- Ingress is a Kubernetes API object that manages external access to services within a cluster, typically HTTP and HTTPS traffic. 
- It defines rules for routing incoming requests to specific internal services based on hostnames and paths. 
- An Ingress Controller is a separate component that runs in the cluster and evaluates these Ingress rules, managing all redirections and acting as the entry point for external requests. 


**Ingress Controller Installation & Cluster Setup**

To install the Ingress Controller in MiniKube, execute
```bash
 minikube add-ons enable ingress
 ```
- This command automatically configures and starts the Kubernetes NGINX implementation of the Ingress Controller, which is suitable for production environments. 
You can verify the Ingress Controller pod is running by using 
```bash
kubectl get pod -n kube-system.
```

**Cluster Entry Point Configuration**

The method for configuring the entry point to your Kubernetes cluster depends on the environment:
- Cloud Service Providers (e.g., AWS, Google Cloud): Typically offer out-of-the-box solutions like virtualized cloud load balancers that redirect external requests to the Ingress Controller. 
- Bare Metal Environment: Requires manual configuration of an entry point, such as an external proxy server (software or hardware solution) with a public IP address and open ports, which then forwards requests to the Ingress Controller. 
This entry point, whether a cloud load balancer or a proxy server, should be the only component externally accessible, enhancing security by keeping internal cluster servers private. 

- The domain name used in Ingress rules must be mapped to the IP address of this entry point (e.g., in a local hosts file for development or DNS for production). 

**Basic Ingress Rule Configuration**
An Ingress rule defines how external traffic for a specific host (domain name) and path (URL path) is forwarded to an internal service. 

The basic structure of an Ingress YAML configuration includes:
```yml
kind: Ingress 
metadata: Contains name and namespace. 
spec: Defines the routing rules. s
host: The domain name (e.g., dashboard.com) that users will      enter in their browser. 
http: Defines HTTP forwarding rules. 
paths: A list of path-based routing rules. s
path: The URL path (e.g., / for all paths, or /analytics). 
backend: Specifies the internal service to which the request is redirected. s
serviceName: The name of the internal Kubernetes service. 
servicePort: The internal port of the service. s
An internal service, unlike an external one, typically uses type: ClusterIP and does not have a nodePort attribute. 

```

**Advanced Ingress Use Cases**
Ingress configurations can handle more complex routing scenarios beyond simple one-to-one forwarding:

- Multiple Paths for the Same Host: A single domain can forward requests to different applications based on the URL path (e.g., myapp.com/analytics to an analytics service, myapp.com/shopping to a shopping service). 

- Subdomains (Multiple Hosts): Different subdomains can route to separate applications (e.g., analytics.myapp.com to an analytics service). Each subdomain is defined as a distinct host in the Ingress rules, typically with a single path. 

 **Default Backend**
   - Ingress supports a default backend that handles requests not explicitly mapped by any defined rule. 
   - If no specific service is found for a request, Kubernetes attempts to forward it to the service defined in the default backend. 
   - This can be used to display custom error messages or redirect users to a homepage when a requested page is not found. s
- To implement this, you create an internal service with a specific name (e.g., default-http-backend) and a corresponding application pod that provides the custom error response. 


**Configuring TLS Certificates for HTTPS**
Ingress makes it straightforward to configure HTTPS connections using TLS certificates. 
- To enable TLS, add a tls attribute above the rules section in your Ingress configuration.

  - host: The domain name for which the TLS certificate is valid. s
  - secretName: A reference to a Kubernetes Secret that stores the TLS certificate and key. 
- The Secret holding the TLS certificate must be of type: kubernetes.io/tls. 
- The data within the secret must have keys named tls.crt (for the certificate content) and tls.key (for the key content), with their respective base64 encoded values. 
- The TLS secret must reside in the same namespace as the Ingress component to be referenced. 



