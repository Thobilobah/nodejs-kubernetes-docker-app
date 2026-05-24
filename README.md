## Project Title: Deploy a Node.js App on Kubernetes Using Docker

---

### Goal:
Deploy a basic "Hello World" Node.js app using Kubernetes on Minikube or Docker Desktop Kubernetes cluster.

---

### Tools Required
-Node.js and npm
-Docker
-Kubernetes (Minikube or Docker Desktop with K8s enabled)
-kubectl (Kubernetes CLI)
-VS Code or any code editor

![kubernetes running](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20175802.png)

![Instancerunning](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20175949.png)

---

### Step by Step Guide

---

### Step 1: Create a Simple Node.js App
```bash
mkdir k8s-node-app && cd k8s-node-app
npm init -y
```

#### Create a file named `index.js`
```bash
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello World from Kubernetes!');
});

const port = process.env.PORT || 3000;
app.listen(port, () => {
  console.log(`App listening on port ${port}`);
});
```
![Index.js file](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20180228.png)

#### Install express
```bash
npm install express
```
![Install express](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20183421.png)

#### Create a `Dockerfile`
```bash
nano Dockerfile
```
```bash
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```
![Create a Dockerfile](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20183707.png)

### Step 2: Build and push the Docker Image
#### If you are using Docker Hub
```bash
docker build -t <your-dockerhub-username>/k8s-node-app .
docker push <your-dockerhub-username>/k8s-node-app
```
![building & pushing docker image](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20184314.png)

![building & pushing docker image](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20190324.png)

### Step 3: Create a Kubernetes Deployment YAML
Create a file named `deployment.yaml`
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app
        image: <your-dockerhub-username>/k8s-node-app
        ports:
        - containerPort: 3000
```

![deployment.yaml file](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20193558.png)

### Step 4: Create Kubernetes Service YAML
create a file named `service.yaml`

![service.yaml 1](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20193629.png)
![service.yaml 1](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20193717.png)

```bash
apiVersion: v1
kind: Service
metadata:
  name: node-app-service
spec:
  type: NodePort
  selector:
    app: node-app
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 30036
```

### Step 5: Deploy to Kubernetes
Start your local Kubernetes cluster (Minikube or Docker Desktop), then:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

![service.yaml 1](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20193800.png)

### Step 6: Access your App
If you're using Minikube, run:
```bash
minikube service node-app-service
```
If using Docker Desktop, visit:
```bash
http://localhost:30036
```
You should see: "Hello World from Kubernetes!"
![Hello World page](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20193828.png)

### Step 7: Verify Everythying is Running
```bash
kubectl get pods
kubectl get services
kubectl describe deployment node-app-deployment
```
![pods verification](https://github.com/Thobilobah/nodejs-kubernetes-docker-app/blob/main/Screenshots%20from%20my%20environment/Screenshot%202026-05-23%20193918.png)











