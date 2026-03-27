# 🚀 Kubernetes WordPress + MySQL 2-Tier Application

## 📌 Overview

This project demonstrates a **production-style deployment of a 2-tier application** on Kubernetes using:

* **WordPress** (Frontend - Deployment)
* **MySQL** (Backend - StatefulSet)

It showcases key Kubernetes concepts like **StatefulSets, Headless Services, ConfigMaps, Secrets, Persistent Volumes, and Horizontal Pod Autoscaling (HPA)**.

---

## 🏗️ Architecture

```text
                🌐 User (Browser)
                        │
                        ▼
              ┌───────────────────┐
              │ WordPress Service │ (NodePort)
              └─────────┬─────────┘
                        │
                        ▼
         ┌──────────────────────────┐
         │ WordPress Deployment (x2)│
         │  - ConfigMap             │
         │  - Secret                │
         └─────────┬────────────────┘
                   │
                   ▼
        ┌────────────────────────────┐
        │ MySQL Headless Service     │
        └─────────┬──────────────────┘
                  │
                  ▼
        ┌────────────────────────────┐
        │ MySQL StatefulSet (1 Pod)  │
        │  - Persistent Volume       │
        │  - Stable DNS              │
        └────────────────────────────┘
```

---

## ⚙️ Tech Stack

* Kubernetes (Minikube / Kind)
* Docker
* WordPress
* MySQL
* YAML (Kubernetes manifests)

---

## 📁 Project Structure

```text
kubernetes-wordpress-2tier-app/
│
├── k8s/
│   ├── 01-namespace.yaml
│   ├── 02-mysql-secret.yaml
│   ├── 03-mysql-headless-service.yaml
│   ├── 04-mysql-statefulset.yaml
│   ├── 05-wordpress-configmap.yaml
│   ├── 06-wordpress-deployment.yaml
│   ├── 07-wordpress-service.yaml
│   ├── 08-hpa.yaml
│
└── README.md
```

---

## 🔑 Key Kubernetes Concepts Used

### 🔹 Namespace

* Logical isolation of resources
* Example: `wordpress-app`

---

### 🔹 Secret

* Stores sensitive data (DB credentials)
* Uses `stringData` for easy input

---

### 🔹 ConfigMap

* Stores non-sensitive config
* Example:

  * DB Host
  * DB Name

---

### 🔹 Headless Service

* `clusterIP: None`
* Enables **stable DNS for StatefulSet**
* Example:

```text
mysql-0.mysql-headless
```

---

### 🔹 StatefulSet (MySQL)

* Stable Pod identity
* Persistent storage using PVC
* Ideal for databases

---

### 🔹 Deployment (WordPress)

* Stateless application
* Scales easily (replicas = 2)
* Uses ConfigMap + Secret

---

### 🔹 Service (NodePort)

* Exposes WordPress externally
* Accessible via:

```text
http://<node-ip>:30080
```

---

### 🔹 Probes (Health Checks)

* **Liveness Probe** → Restarts unhealthy container
* **Readiness Probe** → Controls traffic
* **Startup Probe** → Prevents early restarts

---

### 🔹 Horizontal Pod Autoscaler (HPA)

* Auto-scales WordPress pods
* Based on CPU utilization
* Range: 2 → 10 pods

---

## 🚀 Deployment Steps

### 1️⃣ Create Namespace

```bash
kubectl apply -f k8s/01-namespace.yaml
```

---

### 2️⃣ Deploy MySQL

```bash
kubectl apply -f k8s/02-mysql-secret.yaml
kubectl apply -f k8s/03-mysql-headless-service.yaml
kubectl apply -f k8s/04-mysql-statefulset.yaml
```

---

### 3️⃣ Deploy WordPress

```bash
kubectl apply -f k8s/05-wordpress-configmap.yaml
kubectl apply -f k8s/06-wordpress-deployment.yaml
kubectl apply -f k8s/07-wordpress-service.yaml
```

---

### 4️⃣ Enable Metrics Server (for HPA)

```bash
minikube addons enable metrics-server
```

---

### 5️⃣ Deploy HPA

```bash
kubectl apply -f k8s/08-hpa.yaml
```

---

## 🌐 Access Application

```bash
minikube service wordpress-service -n wordpress-app
```

OR:

```text
http://<node-ip>:30080
```

---

## 🧪 Testing

### ✅ Self-Healing

```bash
kubectl delete pod <wordpress-pod>
```

👉 Pod automatically recreated

---

### ✅ Persistence

* Delete MySQL pod
* Data remains intact (PVC)

---

### ✅ Autoscaling

Generate load:

```bash
kubectl run -it --rm load-generator --image=busybox -- /bin/sh
```

Then:

```bash
while true; do wget -q -O- http://wordpress-service; done
```

👉 Watch scaling:

```bash
kubectl get hpa
```

## 🎯 Key Learnings

* Difference between **StatefulSet vs Deployment**
* Importance of **Headless Services**
* Managing configs using **ConfigMap & Secrets**
* Handling **slow startup apps using probes**
* Implementing **auto-scaling with HPA**
* Designing a **2-tier architecture on Kubernetes**

---

## 🔥 Future Improvements

* Add **Ingress Controller (Nginx)**
* Use **Persistent Volume for WordPress uploads**
* Add **TLS (HTTPS)**
* CI/CD using GitHub Actions
* Helm chart packaging

---

## 👨‍💻 Author

**Vishal Khairnar**
DevOps Engineer | Cloud Enthusiast

---

# ⭐ If you like this project, give it a star!
