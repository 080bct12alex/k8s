# Guide for Kubernetes

- To make a Docker file, build a Docker image, push it to Docker Hub, and use that image in Kubernetes

### Step 1: Create a Dockerfile

- A Dockerfile is a script containing instructions on how to build a Docker image. 

Here's a basic example for a Node.js-based application (Express backend or React frontend).

**Example: Dockerfile for Express Backend**

```bash
# Use official Node.js image as the base image
FROM node:16

# Set the working directory inside the container
WORKDIR /usr/src/app

# Copy the package.json and package-lock.json files
COPY package*.json ./

# Install app dependencies
RUN npm install

# Copy the rest of the application code into the container
COPY . .

# Expose the port your app will run on
EXPOSE 5000

# Define the command to run the app
CMD ["node", "server.js"]
```


**Example: Dockerfile for React Frontend**

```bash
# Build the React app
FROM node:16 AS build

WORKDIR /app

# Install dependencies
COPY package.json ./
RUN npm install

# Copy the rest of the app code
COPY . ./

# Build the app
RUN npm run build

# Serve the app using Nginx
FROM nginx:alpine

# Copy the build directory from the build stage to Nginx
COPY --from=build /app/build /usr/share/nginx/html

# Expose the port Nginx will run on
EXPOSE 80

# Start Nginx to serve the React app
CMD ["nginx", "-g", "daemon off;"]
```


### Step 2: Build the Docker Image

- After creating the Dockerfile for  application (backend, frontend, or both), use the following command to build the Docker image:

```bash
docker build -t yourdockerhubusername/your-app-name:latest .
```

- latest:  
   - A tag for the image (it’s common to use latest, but  can use any tag like v1, v2, etc.)

- . 
  - Refers to the current directory (where the Dockerfile is located).



# Step 3: Log In to Docker Hub

- Before  push the image to Docker Hub, you need to log in:

```bash
docker login
```

- This will prompt for your Docker Hub credentials (username and password).

# Step 4: Push the Docker Image to Docker Hub

- Once the image is built, push it to Docker Hub:

```bash
docker push yourdockerhubusername/your-app-name:latest
```


- This will upload the Docker image to Docker Hub under the specified repository.

# Step 5: Use the Docker Image in Kubernetes

- Now that  Docker image is available in Docker Hub,  use it in  Kubernetes deployment.

**Create a Kubernetes Deployment YAML file(for example, deployment.yaml):**

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 2  # Scale your app horizontally (optional)
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: yourdockerhubusername/your-app-name:latest  # Replace with your Docker Hub image name
        ports:
        - containerPort: 5000  # Port your app listens on
```

**Create a Kubernetes Service YAML file (for example, service.yaml):**

```bash
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - port: 80        # External port
      targetPort: 5000 # Port the container listens on
  type: LoadBalancer  # or ClusterIP (if internal access only)
```

**Apply the Deployment and Service to Kubernetes:**

- Apply the deployment and service YAML files to your Kubernetes cluster:
```bash
kubectl apply -f deployment.yaml
```
```bash
kubectl apply -f service.yaml
```

**Verify the Deployment:**

- Check if the pods are running and the service is exposed:

```bash
kubectl get pods
```

```bash
kubectl get services
```

If you're using LoadBalancer for the service type (in cloud environments like GCP, AWS), you should see an external IP for accessing your app.

# Step 6: Access the App

- Once  Kubernetes deployment is live,  access the app using the service's external IP (if using a LoadBalancer) or via the port on a node if using NodePort.

**Notes:**

- Tagging Images:

  - It's good practice to use meaningful tags for your images (e.g., v1.0, latest, prod, etc.). 
  - This helps you manage different versions of  app in Docker Hub.

- Continuous Deployment (CI/CD):

  - For a streamlined workflow, consider integrating Docker Hub with CI/CD tools like GitHub Actions, GitLab CI, Jenkins, or CircleCI. 
  - This automates the process of building, testing, and deploying your Docker image to both Docker Hub and Kubernetes.

- Private Docker Hub Repository:

  - If you're using a private repository on Docker Hub, you need to authenticate Kubernetes to pull the image. You can use Kubernetes ImagePullSecrets for this.

- Environment Variables:

  - If  app requires environment variables (e.g., MongoDB URI), set them in  Kubernetes deployment file under env or reference Secrets and ConfigMaps.

