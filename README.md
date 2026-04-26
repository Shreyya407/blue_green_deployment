# 🔄 Blue-Green Deployment using DevOps Tools

## 📌 Overview

This project demonstrates the implementation of the **Blue-Green Deployment strategy** using modern DevOps tools. The goal is to achieve **zero-downtime deployment** by maintaining two identical environments and switching traffic between them seamlessly.

---

## 🎯 Objective

To implement Blue-Green Deployment using:

* Docker
* Kubernetes (Minikube)
* Jenkins
* GitHub
* Terraform *(for infrastructure as code)*
* Ansible *(for configuration management)*

---

## 🧠 Concept: Blue-Green Deployment

Blue-Green Deployment uses two environments:

* 🔵 **Blue Environment** → Current live application
* 🟢 **Green Environment** → New version of the application

### 🔁 Workflow:

1. Users are initially routed to **Blue**
2. New version is deployed in **Green**
3. After testing, traffic is switched to **Green**
4. If issues occur, rollback to **Blue**

---

## 🏗️ Architecture

```
User → Service → (Blue / Green Pods)
```

* Kubernetes Service controls traffic
* Label selector decides which version is active

---

## 🛠️ Tools Used

| Tool       | Purpose                     |
| ---------- | --------------------------- |
| Docker     | Containerization            |
| Kubernetes | Deployment & orchestration  |
| Jenkins    | CI/CD automation            |
| GitHub     | Version control             |
| Terraform  | Infrastructure provisioning |
| Ansible    | Server configuration        |

---

## ⚙️ Prerequisites

* Linux/Ubuntu system or EC2 instance
* Docker installed
* Minikube or Kubernetes cluster
* kubectl configured
* Jenkins installed (local or Docker)

---

## 🚀 Implementation Steps

### 1️⃣ Application Setup

A simple web app is created using HTML and served via Nginx.

---

### 2️⃣ Dockerization

Dockerfile is used to containerize the application.

```dockerfile
FROM nginx
COPY . /usr/share/nginx/html
```

---

### 3️⃣ Kubernetes Deployment

#### 🔵 Blue Deployment

* Uses image: `myapp:v1`
* Label: `color: blue`

#### 🟢 Green Deployment

* Uses image: `myapp:v2`
* Label: `color: green`

---

### 4️⃣ Service Configuration

The Kubernetes service routes traffic:

```yaml
selector:
  app: myapp
  color: blue
```

---

### 5️⃣ Deployment Process

#### Deploy Blue:

```bash
kubectl apply -f deployment-blue.yaml
kubectl apply -f service.yaml
```

#### Deploy Green:

```bash
kubectl apply -f deployment-green.yaml
```

---

### 6️⃣ Traffic Switching

```bash
kubectl patch service myapp-service \
-p '{"spec":{"selector":{"app":"myapp","color":"green"}}}'
```

---

### 7️⃣ Rollback

```bash
kubectl patch service myapp-service \
-p '{"spec":{"selector":{"app":"myapp","color":"blue"}}}'
```

---

## 🤖 Jenkins Pipeline

Jenkins automates:

* Building Docker image
* Deploying Green version
* Switching traffic after approval

```groovy
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t myapp:v2 .'
      }
    }
    stage('Deploy Green') {
      steps {
        sh 'kubectl apply -f deployment-green.yaml'
      }
    }
    stage('Switch Traffic') {
      steps {
        input "Switch to Green?"
        sh '''
        kubectl patch service myapp-service \
        -p '{"spec":{"selector":{"app":"myapp","color":"green"}}}'
        '''
      }
    }
  }
}
```

---

## 🌍 Terraform (Infrastructure as Code)

Used to provision EC2 instance:

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

---

## 🔧 Ansible (Configuration Management)

Used to automate installation of Docker and Jenkins on the server.

---

## 📊 Expected Output

* Two environments running simultaneously
* Seamless traffic switching
* No downtime
* Instant rollback capability

---

## ✅ Results

* Successfully deployed Blue and Green environments
* Achieved zero-downtime deployment
* Automated deployment using Jenkins
* Demonstrated complete DevOps workflow

---

## 🧠 Conclusion

Blue-Green Deployment is an effective strategy for achieving reliable and zero-downtime releases. By integrating tools like Docker, Kubernetes, and Jenkins, we can automate deployments and ensure system stability. Terraform and Ansible further enhance the pipeline by enabling infrastructure provisioning and configuration automation.
