# Kubernetes Projects & Applications Repository

A comprehensive collection of Kubernetes deployment examples, troubleshooting guides, and application deployments for learning and reference purposes.

## 📋 Table of Contents

- [Overview](#overview)
- [Projects](#projects)
  - [Kubernetes Fundamentals](#kubernetes-fundamentals)
  - [Application Deployments](#application-deployments)
  - [Troubleshooting Guides](#troubleshooting-guides)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Repository Structure](#repository-structure)
- [Contributing](#contributing)

## 🎯 Overview

This repository contains hands-on Kubernetes examples covering various concepts including:
- Pod deployments and management
- ReplicationControllers and high availability
- Volume management and shared storage
- Service configuration and networking
- Application deployments (NGINX, Tomcat, Django, Java)
- Common troubleshooting scenarios

## 📦 Projects

### Kubernetes Fundamentals

#### 1. All-Commands
**Updated:** 4 months ago  
A comprehensive reference guide containing all essential Kubernetes commands for daily operations and management.

#### 2. Kubernetes Time-Check Pod
**Updated:** 4 months ago  
Demonstrates basic pod creation with a time-check application, useful for understanding pod lifecycle and scheduling.

#### 3. Kubernetes Shared Volumes
**Updated:** 4 months ago  
Illustrates how to implement shared volumes between containers in Kubernetes, covering volume types and mount configurations.

#### 4. Deploy Highly Available Pods with ReplicationController
**Updated:** 4 months ago  
Shows how to use ReplicationControllers to ensure high availability and automatic pod scaling/recovery.

#### 5. Update Deployment and Service in Kubernetes
**Updated:** Last month  
Covers strategies for updating running deployments and services with zero-downtime deployments.

### Application Deployments

#### 6. Deploy an NGINX Application on k8s
**Updated:** 4 months ago  
Step-by-step guide to deploying NGINX web server on Kubernetes, including service exposure and configuration.

**Key Features:**
- Basic NGINX deployment
- Service configuration
- Port mapping
- LoadBalancer/NodePort setup

#### 7. Deploy Tomcat App on Kubernetes
**Updated:** 3 months ago  
Complete guide for deploying Apache Tomcat application server on Kubernetes cluster.

**Key Features:**
- Tomcat container deployment
- Resource allocation
- Environment configuration
- Service exposure

#### 8. Django Notes App
**Updated:** 8 months ago  
A full-stack Django application deployment showcasing Python web application hosting on Kubernetes.

**Technologies:**
- Django framework
- Python runtime
- Database connectivity
- StatefulSet or Deployment configuration

#### 9. Java Quotes App
**Updated:** 8 months ago  
Java-based application deployment demonstrating microservices architecture on Kubernetes.

**Technologies:**
- Java application server
- Container optimization
- Service mesh integration
- External dependencies management

#### 10. MySQL Database
MySQL database deployment configurations and best practices for running stateful applications on Kubernetes.

**Key Features:**
- StatefulSet configuration
- Persistent volume claims
- Secret management for credentials
- Backup and recovery strategies

### Troubleshooting Guides

#### 11. Resolve Pod Deployment Issue
**Updated:** 4 months ago  
Common pod deployment problems and their solutions, including image pull errors, resource constraints, and scheduling issues.

**Covers:**
- ImagePullBackOff errors
- CrashLoopBackOff debugging
- Resource quota issues
- Node affinity problems

#### 12. Resolve VolumeMounts Issue in Kubernetes
**Updated:** 4 months ago  
Troubleshooting guide for volume mounting problems, permission issues, and storage class configurations.

**Covers:**
- Permission denied errors
- Mount path conflicts
- PVC binding issues
- Storage class problems

#### 13. Troubleshoot Deployment Issues in Kubernetes
**Updated:** 3 months ago  
Comprehensive guide for diagnosing and fixing deployment-related problems.

**Covers:**
- Rollout failures
- Container restart loops
- Configuration errors
- Network connectivity issues

## 🔧 Prerequisites

Before working with these projects, ensure you have:

- **Kubernetes Cluster**: v1.20+ (minikube, kind, or cloud provider)
- **kubectl**: Latest stable version
- **Container Runtime**: Docker or containerd
- **Basic Knowledge**: 
  - Container concepts
  - YAML syntax
  - Basic Linux commands
  - Networking fundamentals

### Optional Tools
- **Helm**: For package management
- **k9s**: Terminal-based UI for Kubernetes
- **kubectx/kubens**: For context and namespace switching
- **Lens**: Kubernetes IDE

## 🚀 Getting Started

### 1. Clone the Repository
```bash
git clone <repository-url>
cd <repository-name>
```

### 2. Verify Cluster Access
```bash
kubectl cluster-info
kubectl get nodes
```

### 3. Choose a Project
Navigate to any project directory and follow the specific README instructions:

```bash
cd Deploy-NGINX-App
kubectl apply -f .
```

### 4. Verify Deployment
```bash
kubectl get pods
kubectl get services
kubectl get deployments
```

## 📁 Repository Structure

```
.
├── All-Commands/
│   └── all-commands.md
├── Deploy-HA-Pods-ReplicationController/
│   └── README.md
├── Deploy-Tomcat-App/
│   └── README.md
├── Deploy-NGINX-App/
│   └── README.md
├── Kubernetes-Shared-Volumes/
│   └── README.md
├── Kubernetes-Time-Check-Pod/
│   └── Questions
├── Resolve-Pod-Deployment-Issue/
│   └── webserver-pod.yml
├── Resolve-VolumeMounts-Issue/
│   └── Commands.md
├── Troubleshoot-Deployment-Issues/
│   └── Commands.md
├── Update-Deployment-Service/
│   └── README.md
├── django-notes-app/
│   └── [application files]
├── java-quotes-app/
│   └── [application files]
├── mysql/
│   └── [configuration files]
└── README.md
```

## 📚 Learning Path

### Beginner
1. Start with **All-Commands** for basic kubectl syntax
2. Deploy simple **NGINX Application**
3. Understand pod lifecycle with **Time-Check Pod**

### Intermediate
4. Learn high availability with **ReplicationController**
5. Explore storage with **Shared Volumes**
6. Deploy applications: **Tomcat**, **Django**, **Java**

### Advanced
7. Master troubleshooting guides
8. Implement **Update Deployment strategies**
9. Configure databases with **MySQL** deployment

## 🛠️ Common Commands Reference

### Pod Management
```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/bash
```

### Deployment Management
```bash
kubectl get deployments
kubectl rollout status deployment/<deployment-name>
kubectl rollout history deployment/<deployment-name>
kubectl rollout undo deployment/<deployment-name>
```

### Service Management
```bash
kubectl get services
kubectl expose deployment <deployment-name> --port=80 --type=LoadBalancer
kubectl describe service <service-name>
```

### Troubleshooting
```bash
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl top nodes
kubectl top pods
kubectl describe node <node-name>
```

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-example`)
3. Commit your changes (`git commit -am 'Add new Kubernetes example'`)
4. Push to the branch (`git push origin feature/new-example`)
5. Create a Pull Request

### Contribution Areas
- New application deployments
- Additional troubleshooting guides
- Documentation improvements
- Bug fixes and updates
- Best practices and optimizations

## 📝 Project Timeline

- **8 months ago**: Initial Django and Java applications
- **4 months ago**: Core Kubernetes fundamentals and commands
- **3 months ago**: Tomcat deployment and troubleshooting guides
- **Last month**: Latest deployment updates

## 🔗 Useful Resources

- [Official Kubernetes Documentation](https://kubernetes.io/docs/)
- [Kubernetes Patterns](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [Kubernetes Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 👥 Maintainers

This repository is maintained for educational purposes. For questions or issues, please open a GitHub issue.

---

**Last Updated:** February 2026  
**Status:** Active Development  
**Kubernetes Version Compatibility:** 1.20+
