# 🛒 EasyShop DevSecOps & AWS EKS Deployment

> A production-style DevSecOps and cloud deployment implementation for the EasyShop application using Jenkins, Docker, Amazon ECR, Amazon EKS, Kubernetes, AWS Application Load Balancer, Horizontal Pod Autoscaler, IAM, and CI/CD email notifications.

---

## 🚀 Project Overview

The **EasyShop DevSecOps & AWS EKS Deployment** project demonstrates an end-to-end cloud-native application delivery workflow using **Jenkins CI/CD, Docker, Amazon ECR, Amazon EKS, and Kubernetes**.

The project automates the application delivery lifecycle from source code integration and container image creation to secure image publishing, Kubernetes deployment, external traffic routing, autoscaling, and deployment notifications.

The infrastructure is designed to simulate a real-world DevOps deployment workflow with an emphasis on **automation, containerization, Kubernetes orchestration, AWS cloud services, security, and operational reliability**.

### Key Objectives

* Automate application build and deployment using Jenkins.
* Containerize the application using Docker.
* Build and publish container images to Amazon ECR.
* Deploy the application on Amazon EKS using Kubernetes.
* Expose the application using AWS Application Load Balancer and Kubernetes Ingress.
* Configure Horizontal Pod Autoscaling for application workloads.
* Use AWS IAM for controlled access to cloud resources.
* Integrate security scanning into the CI/CD workflow.
* Send CI/CD pipeline notifications through email.
* Validate application health and Kubernetes deployment status.

---

## 🌐 Live Application

The **EasyShop** application is deployed on **Amazon EKS** and exposed through an **AWS Application Load Balancer**.

<p align="center">
  <img src="./screenshots/01-easyshop-live.png" alt="EasyShop Live Application" width="95%">
</p>

### Deployment Environment

| Component               | Implementation                |
| ----------------------- | ----------------------------- |
| Cloud Platform          | AWS                           |
| Kubernetes              | Amazon EKS                    |
| Container Registry      | Amazon ECR                    |
| Ingress / Load Balancer | AWS Application Load Balancer |
| CI/CD                   | Jenkins                       |
| Containerization        | Docker                        |
| Autoscaling             | Kubernetes HPA                |
| Operating Environment   | Linux                         |

### Application Access

🔗 **Live Application:**
`http://k8s-easyshop-easyshop-8d5e6882e1-807577481.eu-west-1.elb.amazonaws.com`

---

## 🏗️ Solution Architecture

The EasyShop platform follows an automated DevSecOps deployment architecture where application source code moves through CI/CD, containerization, security validation, image publishing, Kubernetes deployment, networking, and autoscaling.

### End-to-End Architecture

<p align="center">
  <img src="./screenshots/02-architecture.png" alt="EasyShop AWS DevSecOps Architecture" width="100%">
</p>

### Architecture Flow

```text
Developer
    │
    ▼
GitHub Repository
    │
    ▼
Jenkins CI/CD
    │
    ├── Checkout
    ├── Build
    ├── Security Scan
    └── Docker Build
             │
             ▼
        Amazon ECR
             │
             ▼
        Amazon EKS
             │
       Kubernetes Workloads
             │
        ┌────┴────┐
        │         │
     Service     HPA
        │
        ▼
 Kubernetes Ingress
        │
        ▼
AWS Application Load Balancer
        │
        ▼
🌐 EasyShop Application
```

### Key Architecture Components

| Layer              | Technology                   | Responsibility                            |
| ------------------ | ---------------------------- | ----------------------------------------- |
| Source Control     | GitHub                       | Application source management             |
| CI/CD              | Jenkins                      | Build and deployment automation           |
| Containerization   | Docker                       | Application packaging                     |
| Container Registry | Amazon ECR                   | Secure image storage                      |
| Orchestration      | Amazon EKS                   | Kubernetes workload management            |
| Networking         | Kubernetes Ingress + AWS ALB | External application access               |
| Scaling            | Kubernetes HPA               | Dynamic pod scaling                       |
| Security           | IAM + Trivy                  | Access control and vulnerability scanning |
| Notifications      | Gmail                        | CI/CD pipeline notifications              |

---

---

## 🔄 DevSecOps CI/CD Pipeline

The EasyShop project uses **Jenkins** as the central CI/CD automation server.

The pipeline automates the application delivery process by retrieving the source code, validating the application, performing security checks, building the Docker image, publishing the image to Amazon ECR, and preparing the application for deployment on Amazon EKS.

### Jenkins Pipeline Architecture

```text
Developer
    │
    ▼
GitHub Repository
    │
    ▼
Jenkins Pipeline
    │
    ├── Checkout Source Code
    │
    ├── Build / Validation
    │
    ├── Security Scan
    │
    ├── Docker Image Build
    │
    ├── Push Image to Amazon ECR
    │
    └── Kubernetes Deployment
             │
             ▼
        Amazon EKS
```

### Jenkins Pipeline Stage View

<p align="center">
  <img src="./screenshots/03-jenkins-pipeline.png" alt="Jenkins CI/CD Pipeline" width="100%">
</p>

### Pipeline Stages

| Stage              | Responsibility                                                   |
| ------------------ | ---------------------------------------------------------------- |
| Source Checkout    | Retrieves the latest source code from GitHub                     |
| Build / Validation | Validates the application and build configuration                |
| Security Scan      | Scans the application or container artifacts for security issues |
| Docker Build       | Creates the application container image                          |
| Image Push         | Publishes the Docker image to Amazon ECR                         |
| Deployment         | Deploys the application workload to Amazon EKS                   |
| Validation         | Verifies the deployment and application availability             |

### CI/CD Benefits

* Automated and repeatable application delivery.
* Reduced manual deployment effort.
* Consistent Docker image creation.
* Integrated security validation.
* Automated image publishing to Amazon ECR.
* Kubernetes-based deployment on Amazon EKS.
* Faster and more reliable application releases.

---

### 🔍 SonarQube Code Quality Analysis

SonarQube is integrated into the Jenkins CI/CD pipeline to perform static code analysis and evaluate the quality and security of the application code before the deployment workflow continues.

The analysis provides visibility into:

* Code quality
* Reliability
* Maintainability
* Security hotspots
* New code issues
* Quality Gate status

### SonarQube Dashboard

<p align="center">
  <img src="./screenshots/04-sonarqube-dashboard.png" alt="SonarQube EasyShop Dashboard" width="100%">
</p>

<p align="center">
  <img src="./screenshots/05-sonarqube-dashboard.png" alt="SonarQube EasyShop Dashboard" width="100%">
</p>


The EasyShop project is continuously analyzed by SonarQube as part of the CI/CD workflow.

### ✅ SonarQube Quality Gate

The latest EasyShop analysis reports a **Passed Quality Gate** with **0 new issues**, allowing the CI/CD workflow to proceed through the remaining stages.

### CI/CD Benefits

* Automated and repeatable application delivery.
* Reduced manual deployment effort.
* Consistent Docker image creation.
* Integrated code quality and security analysis.
* Automated publishing of container images to Amazon ECR.
* Kubernetes-based deployment on Amazon EKS.
* Faster and more reliable application releases.


## 📦 Amazon Elastic Container Registry (ECR)

Amazon Elastic Container Registry (Amazon ECR) is used as the private container registry for the EasyShop application.

The Jenkins CI/CD pipeline builds the application container image, performs the required validation and security checks, and publishes the image to the EasyShop ECR repository.

### Container Image Workflow

```text
Jenkins
   │
   ▼
Docker Build
   │
   ▼
Security Scan
   │
   ▼
Amazon ECR
   │
   ▼
Amazon EKS
```

### ECR Repository

The EasyShop application uses a private Amazon ECR repository to store and manage its container images.

<p align="center">
  <img src="./screenshots/06-ecr-repository.png" alt="Amazon ECR EasyShop Repository" width="100%">
</p>

### Container Images

The ECR repository contains the container images used by the EasyShop Kubernetes deployment.

<p align="center">
  <img src="./screenshots/07-ecr-images.png" alt="EasyShop Container Images in Amazon ECR" width="100%">
</p>

### ECR Configuration

| Configuration     | Value                       |
| ----------------- | --------------------------- |
| Registry          | Amazon ECR Private Registry |
| Repository        | `easyshop`                  |
| AWS Region        | `eu-west-1`                 |
| Encryption        | AES-256                     |
| Tag Mutability    | Mutable                     |
| Deployment Target | Amazon EKS                  |

### ECR Benefits

* Private container image storage.
* Centralized Docker image management.
* Versioned image tags for deployments.
* Native integration with AWS services.
* Container images available for Amazon EKS workloads.

---

## ☸️ Amazon EKS — Kubernetes Runtime Platform

Amazon Elastic Kubernetes Service (Amazon EKS) provides the managed Kubernetes platform used to run the EasyShop application workloads on AWS.

The application containers published to **Amazon ECR** are deployed into the EKS cluster, where Kubernetes manages workload scheduling, service connectivity, application availability, and horizontal scaling.

### 🧩 EKS Deployment Architecture

```text
                 Amazon EKS
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
     Kubernetes            Compute Resources
      Workloads             / Worker Nodes
          │                     │
          └──────────┬──────────┘
                     │
                     ▼
              EasyShop Pods
                     │
          ┌──────────┼──────────┐
          │          │          │
          ▼          ▼          ▼
       Service    Ingress      HPA
          │          │          │
          └──────────┼──────────┘
                     ▼
              AWS ALB / Internet
```

### ☁️ EKS Cluster Overview

The EasyShop application is deployed on an **Amazon EKS cluster in the `eu-west-1` (Ireland) AWS region**.

<p align="center">
  <img src="./screenshots/08-eks-cluster.png" alt="EasyShop Amazon EKS Cluster" width="100%">
</p>

> **Amazon EKS acts as the Kubernetes runtime platform, while Amazon ECR provides the container images consumed by the application workloads.**

### 🖥️ EKS Compute Resources

The cluster compute resources provide the runtime capacity required to execute the EasyShop Kubernetes workloads.

<p align="center">
  <img src="./screenshots/09-eks-compute.png" alt="EasyShop Amazon EKS Compute Resources" width="100%">
</p>

### ⚙️ What EKS Manages

| Kubernetes Layer      | Responsibility                                    |
| --------------------- | ------------------------------------------------- |
| **Pods**              | Run the EasyShop application containers           |
| **Deployments**       | Maintain the desired application replica state    |
| **Services**          | Provide stable communication between workloads    |
| **Ingress**           | Route external HTTP/HTTPS traffic                 |
| **HPA**               | Dynamically adjust application replicas           |
| **Compute Resources** | Provide runtime capacity for Kubernetes workloads |

### 🔄 ECR → EKS Deployment Flow

```text
Docker Image
     │
     ▼
Amazon ECR
     │
     │  Image Pull
     ▼
Amazon EKS
     │
     ▼
Kubernetes Deployment
     │
     ▼
EasyShop Pods
     │
     ├── Service
     ├── Ingress
     └── HPA
```

### 🚀 Why Amazon EKS?

Amazon EKS provides the Kubernetes foundation for the project while integrating the application runtime with AWS-native services such as **IAM, networking, load balancing, and container registry**.

This architecture enables a scalable and repeatable container deployment model while keeping the application configuration managed through Kubernetes manifests.

---

## 🌐 AWS Application Load Balancer & Kubernetes Ingress

The EasyShop application is exposed to the internet through an **AWS Application Load Balancer (ALB)** integrated with **Kubernetes Ingress**.

This networking layer provides a controlled path for external traffic to reach the application workloads running inside the Amazon EKS cluster.

### 🌍 End-to-End Traffic Flow

```text
                    Internet
                       │
                       ▼
        ┌─────────────────────────────┐
        │ AWS Application Load        │
        │ Balancer                    │
        └─────────────────────────────┘
                       │
                       ▼
              Kubernetes Ingress
                  easyshop-ingress
                       │
                       ▼
              Kubernetes Service
                       │
                       ▼
                EasyShop Pods
```

### ⚖️ AWS Application Load Balancer

The AWS Application Load Balancer is configured as an **internet-facing Application Load Balancer** and provides the public entry point for the EasyShop application.

<p align="center">
  <img src="./screenshots/10-aws-alb.png" alt="EasyShop AWS Application Load Balancer" width="100%">
</p>

### ☸️ Kubernetes Ingress

Kubernetes Ingress defines the routing configuration used to connect the AWS load balancer with the EasyShop Kubernetes service.

The ingress is associated with the AWS Load Balancer and exposes the application on **HTTP port 80**.

<p align="center">
  <img src="./screenshots/11-kubernetes-ingress.png" alt="EasyShop Kubernetes Ingress" width="100%">
</p>

### 🔗 Verified Routing

The Kubernetes ingress resolves to the same AWS Load Balancer DNS endpoint used to expose the live application.

```text
AWS ALB
   │
   ▼
easyshop-ingress
   │
   ▼
EasyShop Service
   │
   ▼
EasyShop Pods
```

### ✅ Networking Components

| Component              | Role                            |
| ---------------------- | ------------------------------- |
| **AWS ALB**            | Internet-facing entry point     |
| **Listener : 80**      | Receives HTTP traffic           |
| **Kubernetes Ingress** | Defines application routing     |
| **Kubernetes Service** | Provides stable workload access |
| **EasyShop Pods**      | Serve the application           |

### 🚀 Result

## This integration provides a clean cloud-native traffic path from the public internet to the EasyShop workloads running on Amazon EKS, while keeping the application routing configuration managed through Kubernetes.

## 📈 Horizontal Pod Autoscaling (HPA)

The EasyShop application uses **Kubernetes Horizontal Pod Autoscaler (HPA)** to automatically adjust the number of application pods based on CPU utilization.

This allows the application workload to scale dynamically according to resource demand while maintaining the desired application performance.

### ⚙️ HPA Configuration

The current EasyShop HPA configuration uses:

| Configuration          |                 Value |
| ---------------------- | --------------------: |
| Target CPU Utilization |               **50%** |
| Minimum Replicas       |                 **1** |
| Maximum Replicas       |                **10** |
| Current Replicas       |                 **2** |
| Namespace              |            `easyshop` |
| Target Workload        | `Deployment/easyshop` |

### 📊 HPA Status

The current HPA status shows the application operating at approximately **14% CPU utilization against a 50% target**, with 2 active replicas.

<p align="center">
  <img src="./screenshots/12-hpa-status.png" alt="EasyShop Kubernetes HPA Status" width="100%">
</p>

### 🔍 HPA Configuration Details

The detailed Kubernetes HPA configuration provides visibility into the scaling policy, resource targets, replica limits, and current scaling conditions.

<p align="center">
  <img src="./screenshots/13-hpa-details.png" alt="EasyShop HPA Configuration Details" width="100%">
</p>

### 🔄 Scaling Behavior

```text
                 CPU Utilization
                        │
                        ▼
                HPA Evaluates Load
                        │
              ┌─────────┴─────────┐
              │                   │
          Higher Load         Lower Load
              │                   │
              ▼                   ▼
      Increase Replicas     Reduce Replicas
              │                   │
              └─────────┬─────────┘
                        ▼
                 EasyShop Deployment
```

### 🚀 Scaling Benefits

* Automatically adjusts application replicas based on resource utilization.
* Maintains a minimum of 1 replica.
* Supports scaling up to 10 replicas.
* Reduces the need for manual pod scaling.
* Improves application availability during increased workload demand.
* Uses Kubernetes-native autoscaling capabilities.

---

