# Experiment 1: Hello World Docker Container

### 1. Check Docker

```bash
docker -v
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```

### 2. Create Project

```bash
mkdir docker-hello-world
cd docker-hello-world
```

### 3. Create Dockerfile

```dockerfile
FROM ubuntu:22.04

CMD ["echo", "Hello World"]
```

### 4. Build Image

```bash
docker build -t hello-world-image .
docker images
```

### 5. Run Container

```bash
docker run hello-world-image
```

**Output**

```text
Hello World
```

### 6. Verify Container

```bash
docker ps -a
```

# Experiment 2: Docker Image for a Simple Web Application

### 1. Create Project

```bash
mkdir docker-simple-web
cd docker-simple-web
```

### 2. Create `app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Docker Web App!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### 3. Create `requirements.txt`

```text
flask
```

### 4. Create `Dockerfile`

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

### 5. Build Image

```bash
docker build -t simple-web-app .
docker images
```

### 6. Run Container

```bash
docker run -d -p 5000:5000 simple-web-app
```

### 7. Verify Running Container

```bash
docker ps
```

### 8. Open in Browser

```text
http://localhost:5000
```

### 9. Stop Container

```bash
docker stop <container_id>
docker ps
docker ps -a
```

### 10. Verify

Refresh `http://localhost:5000` in the browser. The web application should no longer be accessible.

# Experiment 3: Docker Container for Python File Operations

### 1. Create Project

```bash
mkdir docker-python-fileops
cd docker-python-fileops
```

### 2. Create `file_ops.py`

```python
# Create and write to file
with open("sample.txt", "w") as file:
    file.write("This file is created inside a Docker container.\n")

# Append data
with open("sample.txt", "a") as file:
    file.write("File operations using Python in Docker.\n")

# Read and display file
with open("sample.txt", "r") as file:
    content = file.read()

print("Contents of the file:")
print(content)
```

### 3. Create `Dockerfile`

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY file_ops.py .

CMD ["python", "file_ops.py"]
```

### 4. Build Image

```bash
docker build -t python-fileops .
docker images
```

### 5. Run Container

```bash
docker run python-fileops
```

**Output**

```text
Contents of the file:
This file is created inside a Docker container.
File operations using Python in Docker.
```

```bash
docker ps -a
```

### 6. Verify File Inside Container

```bash
docker run -it python-fileops /bin/bash
```

Inside the container:

```bash
python file_ops.py
ls -la
cat sample.txt
exit
```
# Experiment 4: Docker Image for a Node.js Static Web Application

### 1. Create Project

```bash
mkdir docker-node-static
cd docker-node-static
```

### 2. Create `index.html`

```html
<h1>Hello from Docker!</h1>
```

### 3. Create `server.js`

```javascript
const http = require("http");
const fs = require("fs");

http.createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end(fs.readFileSync("index.html"));
}).listen(3000);

console.log("Server running on port 3000");
```

### 4. Create `Dockerfile`

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

### 5. Build Image

```bash
docker build -t node-static-app .
docker images
```

### 6. Run Container

```bash
docker run -d -p 3000:3000 node-static-app
```

### 7. Verify Container

```bash
docker ps
```

### 8. Open in Browser

```
http://localhost:3000
```

**Output**

```
Hello from Docker!
```

### 9. Stop Container

```bash
docker stop <container_id>
docker ps
```

# Experiment 5: Docker Compose - Web Server + MySQL

### 1. Create Project

```bash
mkdir docker-compose-web-db
cd docker-compose-web-db
```

### 2. Create `app.py`

```python
from flask import Flask
import mysql.connector

app = Flask(__name__)

@app.route("/")
def hello():
    db = mysql.connector.connect(
        host="db",
        user="root",
        password="root",
        database="testdb"
    )
    cursor = db.cursor()
    cursor.execute("SELECT 'Hello from MySQL inside Docker Compose!'")
    return cursor.fetchone()[0]

app.run(host="0.0.0.0", port=5000)
```

### 3. Create `requirements.txt`

```text
flask
mysql-connector-python
```

### 4. Create `Dockerfile`

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

### 5. Create `docker-compose.yml`

```yaml
version: "3.8"

services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: testdb
```

### 6. Start Containers

```bash
docker-compose up
```

### 7. Verify Containers

(Open another terminal)

```bash
cd docker-compose-web-db
docker-compose ps
```

### 8. Open in Browser

```text
http://localhost:5000
```

**Output**

```text
Hello from MySQL inside Docker Compose!
```

### 9. Stop Containers

```bash
docker-compose down
```

# Experiment 6: Secure Docker Container

### 1. Create Project

```bash
mkdir docker-secure-app
cd docker-secure-app
```

### 2. Create `app.py`

```python
print("Secure Docker Container Running as Non-Root User")
```

### 3. Create `Dockerfile`

```dockerfile
FROM python:3.10-slim

RUN useradd -m secureuser

USER secureuser

WORKDIR /home/secureuser/app

COPY app.py .

CMD ["python", "app.py"]
```

### 4. Build Image

```bash
docker build -t secure-python-app .
docker images
```

### 5. Run Container

```bash
docker run secure-python-app
```

**Output**

```text
Secure Docker Container Running as Non-Root User
```

### 6. Verify User

```bash
docker run -it secure-python-app /bin/bash
whoami
exit
```

**Output**

```text
secureuser
```

### 7. Run Without Port Mapping

```bash
docker run secure-python-app
docker ps -a
```

### 8. Create Private Network

```bash
docker network create secure-net
docker network ls
```

### 9. Run in Private Network

```bash
docker run --network secure-net secure-python-app
```

---
---
---

# Experiment 7: Deploy a Simple Application using Minikube

### 1. Create Project

```bash
mkdir app-in-minikube
cd app-in-minikube
```

### 2. Start Minikube

```bash
minikube start --driver=docker
```

### 3. Verify Cluster

```bash
kubectl get nodes
```

### 4. Create Deployment

```bash
kubectl create deployment web-app --image=nginx
```

### 5. Verify Deployment

```bash
kubectl get deployments
kubectl get pods
```

### 6. Expose Deployment

```bash
kubectl expose deployment web-app --type=NodePort --port=80
```

### 7. Verify Service

```bash
kubectl get services
```

### 8. Open Application

```bash
minikube service web-app
```

Or get the URL and test using:

```bash
curl <minikube-service-url>
```

### 9. Cleanup

```bash
kubectl delete service web-app
kubectl delete deployment web-app

kubectl get services
kubectl get deployments
kubectl get pods
```

### 10. Stop Minikube

```bash
minikube stop
```


# Experiment 8: Kubernetes Network Policies

### 1. Create Project

```bash
mkdir exp8
cd exp8
```

### 2. Start Minikube (Calico)

```bash
minikube start --driver=docker --cni=calico

kubectl get nodes
kubectl get pods -n kube-system
```

### 3. Create Pods

```bash
kubectl run frontend --image=nginx --labels=app=frontend
kubectl run backend --image=nginx --labels=app=backend

kubectl get pods -o wide
```

### 4. Test Communication

```bash
kubectl exec -it frontend -- /bin/bash
```

Inside the pod:

```bash
apt update && apt install curl -y

curl <backend-pod-ip>
```

### 5. Create `deny-all.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress: []
  policyTypes:
    - Ingress
```

Apply:

```bash
kubectl apply -f deny-all.yaml
kubectl get netpol
```

### 6. Test Again

Inside the frontend pod:

```bash
curl <backend-pod-ip>
```

**Result:** Connection fails.

### 7. Create `allow-frontend.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
  policyTypes:
    - Ingress
```

Apply:

```bash
kubectl apply -f allow-frontend.yaml
kubectl get netpol
```

### 8. Test Again

```bash
kubectl exec -it frontend -- /bin/bash

curl <backend-pod-ip>
```

**Result:** Connection succeeds.

### 9. Cleanup

```bash
kubectl delete pod frontend backend

kubectl delete -f deny-all.yaml
kubectl delete -f allow-frontend.yaml

kubectl get pods
kubectl get netpol
```

# Experiment 9: Kubernetes Ingress Controller

### 1. Start Minikube

```bash
minikube start
```

### 2. Enable Ingress

```bash
minikube addons enable ingress

kubectl get pods -n ingress-nginx
```

### 3. Create Deployment

```bash
kubectl create deployment webapp --image=nginx

kubectl get deployments
```

### 4. Expose Service

```bash
kubectl expose deployment webapp --port=80 --type=NodePort

kubectl get services
```

### 5. Create `ingress.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-ingress

spec:
  rules:
    - host: webapp.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: webapp
                port:
                  number: 80
```

### 6. Apply Ingress

```bash
kubectl apply -f ingress.yaml

kubectl get ingress
```

### 7. Configure Local Host

```bash
minikube ip
```

Add the IP to `/etc/hosts`:

```text
<minikube-ip> webapp.local
```

### 8. Access Application

```bash
curl http://webapp.local
```

Or open in browser:

```text
http://webapp.local
```

### 9. Verify

```bash
kubectl get pods
kubectl get svc
kubectl get ingress
```
# Experiment 10: Monitoring Kubernetes using Prometheus & Grafana

### 1. Start Minikube

```bash
minikube start
```

### 2. Add Helm Repository

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update
```

### 3. Install Prometheus & Grafana

```bash
helm install monitoring prometheus-community/kube-prometheus-stack
```

### 4. Verify Pods & Services

```bash
kubectl get pods

kubectl get services
```

### 5. Access Grafana

```bash
kubectl port-forward svc/monitoring-grafana 3000:80
```

Open:

```text
http://localhost:3000
```

### 6. Get Grafana Password

```bash
kubectl get secret monitoring-grafana -o jsonpath="{.data.admin-password}" | base64 --decode
```

**Username**

```text
admin
```

### 7. Access Prometheus

```bash
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090
```

Open:

```text
http://localhost:9090
```

### 8. Test Query

```text
up
```
# Experiment 11: CI/CD Pipeline for Docker Application

### 1. Create Project

```bash
mkdir cicd-demo
cd cicd-demo
```

### 2. Configure Git (One Time)

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

git config --list
```

### 3. Create `app.py`

```python
print("Hello from CI/CD Pipeline")
```

### 4. Create `Dockerfile`

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]
```

### 5. Create `test.sh`

```bash
python app.py | grep "Hello"
```

Make it executable:

```bash
chmod +x test.sh
```

### 6. Initialize Git Repository

```bash
git init

git add .

git commit -m "Initial commit"
```

### 7. Create GitHub Repository & Link It

```bash
git remote add origin https://github.com/<username>/cicd-demo.git

git branch -M master

git push -u origin master
```

### 8. Create GitHub Actions Workflow

Create `.github/workflows/main.yml`

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - master

jobs:
  build-test-deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Check Docker
        run: docker --version

      - name: Build Docker Image
        run: docker build -t myapp .

      - name: Run Test
        run: docker run myapp

      - name: Run Container
        run: docker run myapp
```

### 9. Push Workflow

```bash
git add .

git commit -m "Added CI/CD pipeline"

git push
```

### 10. Verify Pipeline

Open:

```text
GitHub → Actions
```

**Output**

```text
Hello from CI/CD Pipeline
```

### 11. Test Pipeline Again

Modify `app.py`:

```python
print("Welcome to automated CI/CD Pipeline")
```

Then push:

```bash
git add .

git commit -m "Updated application"

git push
```
# Experiment 12: Docker Swarm

### 1. Create Project

```bash
mkdir exp12
cd exp12
```

### 2. Initialize Swarm

```bash
docker swarm init
```

### 3. Verify Manager Node

```bash
docker node ls
```

### 4. Create Service

```bash
docker service create --name webapp -p 8080:80 nginx
```

### 5. Verify Service

```bash
docker service ls
docker service ps webapp
```

### 6. Open Application

```text
http://localhost:8080
```

### 7. Scale Service

```bash
docker service scale webapp=3
docker service ps webapp
```

### 8. Remove Default Service

```bash
docker service rm webapp
```

### 9. Create Service (Show Container Hostname)

```bash
docker service create --name webapp --replicas 3 -p 8080:80 nginx sh -c "echo Container: \$(hostname) > /usr/share/nginx/html/index.html && nginx -g 'daemon off;'"
```

### 10. Test Load Balancing

Open:

```text
http://localhost:8080
```

Or:

```bash
curl localhost:8080
```

Refresh multiple times to see different container hostnames.

### 11. Verify

```bash
docker service ls
docker service ps webapp
docker ps
```

### 12. Cleanup

```bash
docker service rm webapp
docker swarm leave --force
```
