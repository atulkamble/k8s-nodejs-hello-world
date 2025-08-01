setup for deploying a simple **Node.js web application** using Docker and Kubernetes. 

---

### 📁 **Project Structure:**

```
k8s-nodejs-hello-world/
├── app/
│   ├── Dockerfile
│   └── server.js
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
├── .dockerignore
├── .gitignore
└── README.md
```

---

### 🚀 Step-by-Step Instructions:

#### 1. **Create Node.js App**

📄 `app/server.js`:

```js
const http = require('http');
const port = process.env.PORT || 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello from Kubernetes!\n');
});

server.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});
```

📄 `app/Dockerfile`:

```Dockerfile
FROM node:18-alpine

WORKDIR /app
COPY server.js .

EXPOSE 3000
CMD ["node", "server.js"]
```

#### 2. **.dockerignore**

📄 `.dockerignore`:

```
node_modules
npm-debug.log
```

#### 3. **Kubernetes Deployment Config**

📄 `k8s/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-node-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-node
  template:
    metadata:
      labels:
        app: hello-node
    spec:
      containers:
      - name: hello-node
        image: <your-dockerhub-username>/hello-node:latest
        ports:
        - containerPort: 3000
```

📄 `k8s/service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-node-service
spec:
  type: LoadBalancer
  selector:
    app: hello-node
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
```

---

### 📦 Build & Push Docker Image

```bash
# Inside project root
cd app
docker build -t <your-dockerhub-username>/hello-node:latest .
docker push <your-dockerhub-username>/hello-node:latest
```

---

### ☸️ Deploy to Kubernetes

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

Check the service:

```bash
kubectl get services
```

If using **Minikube**, run:

```bash
minikube service hello-node-service
```

---

### 📝 Sample README.md

```markdown
# k8s-nodejs-hello-world

A basic Node.js app deployed with Kubernetes.

## Features

- Node.js HTTP server
- Dockerized
- Kubernetes Deployment & Service YAMLs

## Quick Start

1. Build & Push Docker Image
2. Apply Kubernetes configs
3. Access via LoadBalancer/Minikube

```

---
