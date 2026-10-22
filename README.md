# Kubernetes Three-Tier Application with Config Management

## 📌 Project Overview

This project demonstrates how to deploy a three-tier application on Kubernetes using proper configuration management and separation of application components.

The application consists of three layers:

- **Frontend:** Nginx web server.
- **Backend:** Python Flask application.
- **Database:** MariaDB.

Kubernetes resources are used to manage application deployment, internal communication, configuration, secrets, and persistent database storage.

## 🏗️ Architecture

```text
                    User
                     |
                     v
              Frontend Service
                     |
              +------+------+
              |             |
              v             v
         Frontend Pod   Frontend Pod
              |
              v
         Backend Service
              |
         +----+----+
         |         |
         v         v
    Backend Pod  Backend Pod
         |
         v
    Database Service
         |
         v
     MariaDB Pod
         |
         v
   Persistent Storage
```

### Configuration Management

```text
ConfigMap
   |
   +-- Database host
   +-- Database port
   +-- Database name

Secret
   |
   +-- Database username
   +-- Database password
```

## 🎯 Project Objectives

- Deploy a three-tier application using Kubernetes.
- Understand Deployments, Pods, and Services.
- Configure communication between application tiers.
- Separate application configuration from container images.
- Store sensitive credentials in Kubernetes Secrets.
- Use PersistentVolumeClaims for database storage.
- Run multiple frontend and backend replicas.
- Understand Kubernetes service discovery.
- Practice application troubleshooting using `kubectl`.

## 🛠️ Technologies Used

- Linux (Ubuntu)
- Kubernetes
- Minikube
- Docker
- Nginx
- Python Flask
- MariaDB
- YAML
- kubectl
- Git and GitHub

## 📂 Project Structure

```text
three-tier-app/
├── README.md
├── namespace.yaml
├── configmap.yaml
├── secret.yaml
├── frontend/
│   ├── deployment.yaml
│   └── service.yaml
├── backend/
│   ├── deployment.yaml
│   └── service.yaml
└── database/
    ├── deployment.yaml
    ├── service.yaml
    └── pvc.yaml
```

## ⚙️ Prerequisites

Before starting, make sure you have installed:

- Docker
- Minikube
- kubectl

Verify the installations:

```bash
docker --version
minikube version
kubectl version --client
```

Start Minikube:

```bash
minikube start
```

Check the cluster:

```bash
kubectl get nodes
```

## 🚀 Deployment Instructions

### 1. Create the Namespace

```bash
kubectl apply -f namespace.yaml
```

Verify:

```bash
kubectl get namespaces
```

### 2. Create the ConfigMap

```bash
kubectl apply -f configmap.yaml
```

The ConfigMap stores non-sensitive application configuration, including the database hostname, port, and database name.

### 3. Create the Secret

```bash
kubectl apply -f secret.yaml
```

The Secret provides database credentials to the application containers.

**Security note:** Do not commit real passwords or other credentials to GitHub. Use placeholders in example manifests and provide actual credentials securely during deployment. Kubernetes Secrets also require appropriate access controls and encryption at rest for production environments.

### 4. Deploy the Database

Create the database storage:

```bash
kubectl apply -f database/pvc.yaml
```

Deploy MariaDB:

```bash
kubectl apply -f database/deployment.yaml
```

Create the database Service:

```bash
kubectl apply -f database/service.yaml
```

Check the database resources:

```bash
kubectl get pods,services,pvc -n three-tier
```

### 5. Deploy the Backend

Make sure the Flask backend Docker image is available to your Kubernetes cluster and that the image name matches `backend/deployment.yaml`.

Deploy the backend:

```bash
kubectl apply -f backend/deployment.yaml
```

Create the backend Service:

```bash
kubectl apply -f backend/service.yaml
```

The backend uses environment variables sourced from the ConfigMap and Secret to connect to MariaDB.

The database Service is accessible from the same namespace using:

```text
database:3306
```

### 6. Deploy the Frontend

Deploy the frontend:

```bash
kubectl apply -f frontend/deployment.yaml
```

Create the frontend Service:

```bash
kubectl apply -f frontend/service.yaml
```

If the frontend Service uses NodePort, access it through Minikube:

```bash
minikube service frontend -n three-tier
```

**Note:** The frontend must be configured to communicate with the backend correctly. A browser-based frontend generally cannot resolve the internal Kubernetes hostname `backend` directly. Use an appropriate reverse proxy or externally accessible API endpoint for browser requests.

## 🔍 Verification and Testing

Check all Pods:

```bash
kubectl get pods -n three-tier
```

Check Deployments:

```bash
kubectl get deployments -n three-tier
```

Check Services:

```bash
kubectl get services -n three-tier
```

Check PersistentVolumeClaims:

```bash
kubectl get pvc -n three-tier
```

Check application logs:

```bash
kubectl logs deployment/backend -n three-tier
```

```bash
kubectl logs deployment/database -n three-tier
```

Inspect a resource when troubleshooting:

```bash
kubectl describe pod <pod-name> -n three-tier
```

## 🧠 Key Concepts Learned

### 1. Deployments

Deployments manage application replicas and help maintain the desired number of Pods.

### 2. Services

Services provide stable network endpoints for communication between application components.

### 3. ConfigMaps

ConfigMaps separate non-sensitive configuration from application images.

### 4. Secrets

Secrets provide a mechanism for supplying sensitive configuration to applications.

### 5. PersistentVolumeClaims

PersistentVolumeClaims request persistent storage so database files can survive Pod replacement, provided the underlying storage is configured correctly.

### 6. Service
