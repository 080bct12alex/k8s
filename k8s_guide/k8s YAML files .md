# Common Kubernetes YAML files for ConfigMap, Secret, Deployment, and Service that  use in various Kubernetes applications.

**1. ConfigMap YAML**

-  used to store non-sensitive configuration data in key-value pairs that can be referenced by pods.

```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_MODE: "production"  # Example config data
  LOG_LEVEL: "info"
  API_URL: "https://api.example.com"
```

**2. Secret YAML**

- used to store sensitive data like passwords, tokens, etc. 
- The data must be base64-encoded.

```bash
apiVersion: v1
kind: Secret
metadata:
  name: mongo-credentials
type: Opaque
data:
  mongo-uri: bW9uZ29kYjpmYWtzZHlhMzEyMzpTb21lUGFzc3dvcmQxMjM=  # base64 encoded Mongo URI
```

= To encode a secret value, use this command:
```bash
echo -n "your-secret-value" | base64
```


**3. Deployment YAML**

- A Deployment manages the deployment of Pods and ensures the specified number of replicas are running.

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
spec:
  replicas: 3  # Number of pods
  selector:
    matchLabels:
      app: app-name
  template:
    metadata:
      labels:
        app: app-name
    spec:
      containers:
        - name: app-container
          image: my-docker-repo/my-app:latest
          ports:
            - containerPort: 8080
          env:
            - name: MONGO_URI
              valueFrom:
                secretKeyRef:
                  name: mongo-credentials  # Reference to the Secret
                  key: mongo-uri
            - name: APP_MODE
              valueFrom:
                configMapKeyRef:
                  name: app-config  # Reference to the ConfigMap
                  key: APP_MODE
            - name: LOG_LEVEL
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: LOG_LEVEL
```

**4. Service YAML**

- A Service exposes the app to the network, allowing internal or external communication. 
- It can expose your app using different types such as ClusterIP, NodePort, or LoadBalancer.

```bash
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: app-name  # Matches labels of pods
  ports:
    - protocol: TCP
      port: 80           # Port exposed by the service
      targetPort: 8080   # Port your container listens on
  type: LoadBalancer    # Expose externally via cloud load balancer (use ClusterIP for internal only)
```


**Putting It All Together**

- use these YAMLs in sequence to deploy a complete Kubernetes setup, like :

- Create Resources:
```bash
kubectl apply -f secret.yaml
kubectl apply -f configmap.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

- Verify:
```bash
kubectl get pods            # To see if your pods are running
kubectl get services        # To check the services exposed
kubectl get secrets         # Verify the secret is created
kubectl get configmaps      # Verify the config map is created
```

- Scale the Deployment (if needed):

```bash
kubectl scale deployment app-deployment --replicas=3
```



