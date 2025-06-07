# Kubernetes-Based Self-Hosted CI/CD Platform

## Overview

This project demonstrates a **complete self-hosted CI/CD infrastructure** deployed inside Kubernetes. It uses **Jenkins**, **Nexus Repository**, and **Docker-in-Docker (DinD) agents** to simulate a full DevOps workflow from build → store → deploy, all on a private, production-like cluster.

It reflects how DevOps teams run internal CI/CD systems without relying on GitHub Actions or cloud-managed tools.

---

## Tech Stack

- **Kubernetes** (on-prem / Minikube / EKS)
- **Helm** for deployment
- **Jenkins** (Helm chart)
- **Docker-in-Docker** Jenkins agents
- **Nexus Repository OSS** for image/artifact storage
- **Helm chart** for microservice deployment

---

## Project Structure

k8s-cicd-infrastructure/
- app/ # Sample Flask app
- helm-app/ # Helm chart for app deployment
- jenkins/ # Jenkins Helm values
- nexus/ # Nexus deployment YAML
- jenkins-pipeline/ # Jenkinsfile and seed jobs
- README.md

---

## Setup Instructions

### 1. Install Jenkins via Helm

```
helm repo add jenkins https://charts.jenkins.io
helm install jenkins jenkins/jenkins -n jenkins --create-namespace -f jenkins/values.yaml
```

Jenkins will expose a UI on NodePort, login using the secret from:
```
kubectl get secret jenkins -n jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode
```

### 2. Create namespace and Deploy Nexus Repository
```
kubectl create ns nexus
kubectl apply -f nexus/nexus.yaml
```
Then port-forward:
```
kubectl port-forward svc/nexus 8081:8081 -n nexus
```
Login: admin / admin123

### 3. Build + Deploy via Jenkins
- Load the Jenkinsfile from /jenkins-pipeline/
- Jenkins will:
   - Build the app
   - Push image to Nexus Docker repo
   - Deploy to Kubernetes using Helm
   
---

#### Sample Pipeline Stages
- Build Docker image from /app
- Tag + Push to Nexus Docker registry
- Deploy using helm upgrade --install

---
