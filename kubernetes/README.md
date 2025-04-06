# Kubernetes Setup with Minikube on Rocky Linux

This guide walks you through setting up a local Kubernetes environment on Rocky Linux using Minikube and deploying a Spring Boot application.

---

## ✏️ Step 1: Install Kubernetes Environment on Rocky Linux

### 🧱 1.1 Install Required Tools

```bash
sudo dnf install -y curl wget conntrack
```

### 🐳 1.2 Install Docker

```bash
sudo dnf install -y dnf-plugins-core
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf install -y docker-ce docker-ce-cli containerd.io

sudo systemctl start docker
sudo systemctl enable docker
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
kubectl create namespace springboot-app
```

### ⚙️ 3.2 Create Deployment YAML

**deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springboot-deployment
  namespace: springboot-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: springboot
  template:
    metadata:
      labels:
        app: springboot
    spec:
      containers:
        - name: springboot
          image: springboot-app:latest
          imagePullPolicy: Never
          ports:
            - containerPort: 8081
```

### 🌐 3.3 Create Service YAML

**service.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: springboot-service
  namespace: springboot-app
spec:
  selector:
    app: springboot
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
  name: springboot-ingress
  namespace: springboot-app
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: springboot.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: springboot-service
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
127.0.0.1 springboot.local
```

### 🌐 Open in Browser

```
http://springboot.local
```

---

## 🪤 Step 6: Clean Up

```bash
kubectl delete -f ingress.yaml
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml
kubectl delete namespace springboot-app
```

---

## 📸 Optional: Take Screenshots

- `minikube start`
- `kubectl get all -n springboot-app`
- Spring Boot App in browser at `http://springboot.local`

---

> Created for local Kubernetes development on Rocky Linux using Minikube.

