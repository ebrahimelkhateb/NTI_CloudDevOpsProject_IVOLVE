# Deploying a Java Web Application on Kubernetes
This guide walks you through setting up a local Kubernetes environment using Minikube and deploying a Java Web application.
---
## ✏️ Step 1: Install Kubernetes Environment on Rocky Linux
### 🧱 1.1 Install Required Tools
```bash
sudo dnf install -y curl wget conntrack
```
### 📦 1.3 Install Kubectl
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```
### 🚀 1.4 Install Minikube
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
sudo rpm -ivh minikube-latest.x86_64.rpm
```
---
## ▶️ Step 2: Start Minikube
```bash
minikube start --driver=docker --memory=1200mb
```
Check Minikube status:
```bash
minikube status
```
---
## 🚀 Step 3: Deploy Spring Boot App to Kubernetes
### 📂 3.1 Create Namespace
```bash
kubectl create namespace ivolve
```
### ⚙️ 3.2 Create Deployment YAML
**deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
  namespace: ivolve
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: ebrahimelkhateb/my-java-application
        ports:
        - containerPort: 8081
```
### 🌐 3.3 Create Service YAML
**service.yaml**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: app-service
  namespace: ivolve
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8081
  type: ClusterIP
```
### 🌍 3.4 Create Ingress YAML
Enable ingress addon:
```bash
minikube addons enable ingress
```
**ingress.yaml**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  namespace: ivolve
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    kubernetes.io/ingress.class: "nginx"
spec:
  ingressClassName: nginx
  rules:
  - host: myjava-app.ivolve.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app-service
            port:
              number: 80
```
---
## 🔄 Step 4: Apply YAML Files
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
```
---
## 🔍 Step 5: Test the App
### 📑 Add Host Entry
```bash
sudo nano /etc/hosts
```
Add this line:
```
<minikube-ip> myjava-app.ivolve.com
```
### 🌐 Open in Browser
```
http://myjava-app.ivolve.com
```
---
## 📊 Sample Application
Once deployed, you should see the Spring Boot application running. The application shows different Pod IPs as requests are load-balanced between replicas:

### Pod 1 (IP: 10.244.0.8)
![Spring Boot App - Pod 1](/api/placeholder/400/320)

### Pod 2 (IP: 10.244.0.9)
![Spring Boot App - Pod 2](/api/placeholder/400/320)

This confirms our Kubernetes deployment is working correctly with multiple replicas handling traffic through the ingress controller.
