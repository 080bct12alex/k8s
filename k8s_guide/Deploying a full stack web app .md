# Deploying a full stack web app with React , Express , Nginx , and MongoDB within Kubernetes 

### Components need to set up :

- Frontend (React): React will be served by Nginx (as a static site).

- Backend (Express): The Express app ; RESTful API built using Express will communicate with MongoDB.

- MongoDB: A MongoDB database that can be either managed (MongoDB Atlas) or self-hosted in your Kubernetes cluster.

- Nginx: for serving the React frontend app and reverse proxying requests to the Express backend API.

## Steps to create the appropriate Kubernetes configurations.
### Kubernetes Resources
- MongoDB (Cloud or Self-Hosted)
- Express Backend
- React Frontend
- Nginx to serve React and reverse proxy Express

### Steps to structure these resources in separate YAML files:
**NOTE** 
 - Define Secrets, ConfigMaps, Deployments, and Services in separate YAML files      
 (deployments and services can add in same file) 
    for clarity ,scalability .

# MongoDB Setup

 **Option 1: MongoDB Atlas (Cloud Database)**

   - MongoDB Atlas for a fully-managed cloud database. Set up a connection URI and store it in a Kubernetes Secret.


**1**. **Secret YAML** **(mongo-secret.yaml)** 

- yaml file name can be anything but put meaningful

- This file contains the sensitive information, like MongoDB credentials, stored securely in a Kubernetes Secret.

```bash
apiVersion: v1
kind: Secret
metadata:
  name: mongo-credentials
type: Opaque
data:
  # MongoDB URI (encoded in base64 format)
  mongo-uri: bW9uZ29kYjpmYWtzZHlhMzEyMzpTb21lUGFzc3dvcmQxMjM=
```

- The mongo-uri value in the example above is base64-encoded. 

   - To generate the base64-encoded MongoDB URI string  , run  Command :

```bash
 echo -n "mongodb+srv://yourusername:yourpassword@cluster0.mongodb.net/test?retryWrites=true&w=majority" | base64
```
   

**or**

```bash
kubectl create secret generic mongo-credentials \
  --from-literal=mongo-uri="mongodb+srv://yourusername:yourpassword@cluster0.mongodb.net/yourdb?retryWrites=true&w=majority"
```

 **2. ConfigMap YAML (app-configmap.yaml)**

- This file contains non-sensitive configuration data like the app mode (production) or log level.

```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  app_mode: "production"
  log_level: "info"
```

- can add more keys/values here depending on your application's configuration needs (e.g., API_URL, TIMEZONE)



**3. Deployment YAML (app-deployment.yaml)**

- This file contains the configuration for app’s deployment, where the secrets and config values are injected as environment variables.

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: your-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: your-app

  template:
    metadata:
      labels:
        app: your-app
    spec:
      containers:
        - name: your-app-container
          image: your-docker-image:latest  # Replace with your Docker image

          env:
            # Reference to the Secret for MongoDB URI

            - name: MONGO_URI
              valueFrom:
                secretKeyRef:
                  name: mongo-credentials
                  key: mongo-uri
            #  Reference to the ConfigMap for app settings

            - name: APP_MODE
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: app_mode
            - name: LOG_LEVEL
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: log_level
```

**Note:** 

    Ensure your Docker image is built and pushed to a container registry (Docker Hub, AWS ECR, etc.) and accessible to the Kubernetes cluster.


- This file configures  app's deployment in Kubernetes. It injects environment variables for MongoDB URI (from the Secret) and app settings (from the ConfigMap).


**4. Service YAML (app-service.yaml)**

- This file defines a Kubernetes Service to expose your app to the Kubernetes network and, depending on configuration, internally or externally(e.g., using a LoadBalancer).

```bash
apiVersion: v1
kind: Service
metadata:
  name: your-app-service
spec:
  selector:
    app: your-app
  ports:
    - protocol: TCP
      port: 8080       # Port that the service exposes
      targetPort: 8080 # Port the container is listening on
  type: ClusterIP     # You can change this to   LoadBalancer or NodePort if needed

ClusterIP: Exposes the service only inside the cluster (default type).

LoadBalancer: Exposes the service externally (usually for cloud providers).

NodePort: Exposes the service on each node’s IP at a static port.
```

**5. Apply the Files**

- Once setup YAML files for the Secret, ConfigMap, Deployment, and Service,  apply them in sequence:

- Apply the Secret:
```bash
kubectl apply -f mongo-secret.yaml
```

- Apply the ConfigMap:
```bash
kubectl apply -f app-configmap.yaml
```

- Apply the Deployment:

```bash
kubectl apply -f app-deployment.yaml
```


- Apply the Service:

```bash
kubectl apply -f app-service.yaml
```

- This will deploy  application, configure it with the MongoDB connection string from the secret, use the app settings from the config map, and expose it via a service.


**6. Verify the Resources**
 - can verify that all resources are created and running by checking the status of your Kubernetes objects:

**Check Pods:**
- ensure that the app is running

```bash
kubectl get pods
```

**Check Services:**
- verify that the service is exposed and has an external IP if applicable

```bash
kubectl get services
```

**Check Deployments:**
- confirm  deployment is running

```bash
kubectl get deployments
```

**Check Secrets:**
- to ensure the MongoDB URI secret was created

```bash
kubectl get secrets
```

**Check ConfigMaps:**
- ensure the app's configuration was created

```bash
kubectl get configmaps
```


**Summary**

- separate YAML files for each resource:

  - Secret (mongo-secret.yaml) for sensitive data (MongoDB URI).

  - ConfigMap (app-configmap.yaml) for non-sensitive configuration data.

  - Deployment (app-deployment.yaml) to deploy your application, injecting the secrets and config map as environment variables.

  - Service (app-service.yaml) to expose your app.

- Apply each file using kubectl apply -f <filename>.yaml to deploy them into your Kubernetes cluster.


**Option 2: Self-hosted MongoDB (in Kubernetes)**

- If  prefer to self-host MongoDB within Kubernetes
Then,  can reference it in  backend configuration.


**Final Notes**

- Scaling:  
  -  easily scale the deployment by changing the replicas field in the Deployment YAML. 
  - For example, changing replicas: 1 to replicas: 3 will create three replicas of your app.

- Persistence: 
   - If using self-hosted   MongoDB (not MongoDB Atlas), make sure to set up persistent storage in Kubernetes for the database. 
  
   - using Persistent Volumes and Persistent Volume Claims.

- Environment-Specific Configurations: 

   - For multiple environments (e.g., dev,  production), you can create different ConfigMaps and Secrets for each environment and reference them accordingly in different deployments.

- Monitor Logs: 
  - After deploying, check the logs for the pods to ensure the app is running as expected:
  
```bash
 kubectl logs <pod-name>
```

# 2. Express Backend Setup

Here’s a basic Express API setup to connect to MongoDB (MongoDB URI coming from the environment variable).

**Express Backend Code (server.js)**


```bash
const express = require('express');
const mongoose = require('mongoose');

const app = express();
const PORT = process.env.PORT || 5000;

// MongoDB connection string from environment variable
const mongoURI = process.env.MONGO_URI;
mongoose.connect(mongoURI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connected to MongoDB'))
  .catch((err) => console.error('MongoDB connection error:', err));

app.get('/', (req, res) => {
  res.send('Express API is working!');
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

**Backend Dockerfile(Dockerfile)**

```bash
# Use the official Node.js image
FROM node:16

# Set the working directory
WORKDIR /usr/src/app

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy all source code
COPY . .

# Expose port for backend
EXPOSE 5000

# Run the app
CMD ["node", "server.js"]
```



**Kubernetes Deployment for Express Backend (express-deployment.yaml)**

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: express-backend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: express-backend
  template:
    metadata:
      labels:
        app: express-backend
    spec:
      containers:
        - name: express-backend
          image: your-docker-repo/express-backend:latest
          ports:
            - containerPort: 5000
          env:
            - name: MONGO_URI
              valueFrom:
                secretKeyRef:
                  name: mongo-credentials
                  key: mongo-uri
---
apiVersion: v1
kind: Service
metadata:
  name: express-backend-service
spec:
  selector:
    app: express-backend
  ports:
    - port: 5000
      targetPort: 5000
  type: ClusterIP
```


# 3. React Frontend Setup

**React App Code (src/App.js)**

```bash
import React, { useEffect, useState } from 'react';

function App() {
  const [message, setMessage] = useState('');

  useEffect(() => {
    fetch('/api')  // Assuming backend is at /api endpoint
      .then(res => res.text())
      .then(data => setMessage(data))
      .catch(err => console.error(err));
  }, []);

  return (
    <div>
      <h1>React App</h1>
      <p>{message}</p>
    </div>
  );
}

export default App;
```


**React Dockerfile (Dockerfile)**

```bash
# Build the React app
FROM node:16 as build

WORKDIR /app

# Install dependencies
COPY package.json ./
RUN npm install

# Copy the rest of the app
COPY . .

# Build the app for production
RUN npm run build


# Serve the app using Nginx
FROM nginx:alpine

# Copy the build from the previous stage
COPY --from=build /app/build /usr/share/nginx/html

# Expose port 80 for the Nginx server
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```


# 4. Nginx Setup

**Nginx Configuration (nginx.conf)**

```bash
server {
    listen 80;
    
    # Serve the React app
    location / {
        root /usr/share/nginx/html;
        try_files $uri /index.html;
    }

    # Reverse proxy for the Express backend API
    location /api {
        proxy_pass http://express-backend-service:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```


**Nginx Dockerfile (Dockerfile)**

```bash
# Use the official Nginx image
FROM nginx:alpine

# Copy the custom Nginx config file
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port 80
EXPOSE 80
```



**Kubernetes Deployment for Nginx (nginx-deployment.yaml)**

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: your-docker-repo/nginx:latest
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer

```

# 5. Apply the Kubernetes Resources

Finally, apply all the Kubernetes resources to the cluster:

```bash
kubectl apply -f mongo-credentials.yaml    
```

```
kubectl apply -f express-deployment.yaml
```

```
kubectl apply -f react-deployment.yaml
```

```
kubectl apply -f nginx-deployment.yaml
```

```
kubectl apply -f express-service.yaml
```

```
kubectl apply -f nginx-service.yaml
```

**Summary of Resources:**

- MongoDB:
  - Either self-hosted or using MongoDB Atlas.

- Express Backend: 
  - Deployed as a Kubernetes Deployment.

- React Frontend: 
  - Built and served using Nginx in Kubernetes.

- Nginx: 
   - Acts as a reverse proxy to serve the React app and proxy backend API requests.

**Additional Considerations:**

- Networking:
  - Ensure the backend API and frontend can communicate through Kubernetes services.

- Scaling: 
  -  scale the Express app and Nginx if needed by modifying the replicas field.

- MongoDB Connection:
  - If using MongoDB Atlas, ensure your Kubernetes cluster's IP is whitelisted.

     

This setup isa clean and scalable architecture, with clear separation between different components of  app (frontend, backend, database).
