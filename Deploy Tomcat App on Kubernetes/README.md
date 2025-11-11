# Tomcat Application Kubernetes Deployment

This repository contains Kubernetes manifests for deploying a Tomcat application on a Kubernetes cluster.

## Overview

This deployment sets up a Java-based Tomcat application with the following components:
- Dedicated namespace for isolation
- Deployment with single replica
- NodePort service for external access

## Prerequisites

- Kubernetes cluster (already configured)
- `kubectl` CLI tool configured to work with the cluster
- Access to `jump_host` with proper kubectl configuration

## Architecture

```
tomcat-namespace-datacenter
├── Deployment: tomcat-deployment-datacenter
│   └── Pod: tomcat-container-datacenter
│       └── Image: gcr.io/kodekloud/centos-ssh-enabled:tomcat
│       └── Port: 8080
└── Service: tomcat-service-datacenter
    └── Type: NodePort
    └── NodePort: 32227
```

## Components

### 1. Namespace
- **Name**: `tomcat-namespace-datacenter`
- **Purpose**: Provides logical isolation for the Tomcat application resources

### 2. Deployment
- **Name**: `tomcat-deployment-datacenter`
- **Namespace**: `tomcat-namespace-datacenter`
- **Replicas**: 1
- **Container Name**: `tomcat-container-datacenter`
- **Image**: `gcr.io/kodekloud/centos-ssh-enabled:tomcat`
- **Container Port**: 8080
- **Labels**: `app: tomcat-datacenter`

### 3. Service
- **Name**: `tomcat-service-datacenter`
- **Namespace**: `tomcat-namespace-datacenter`
- **Type**: NodePort
- **Port**: 8080 (Service Port)
- **TargetPort**: 8080 (Container Port)
- **NodePort**: 32227 (External Access Port)
- **Selector**: `app: tomcat-datacenter`

## Deployment Instructions

### Step 1: Apply the Configuration

```bash
kubectl apply -f tomcat-deployment.yaml
```

Expected output:
```
namespace/tomcat-namespace-datacenter created
deployment.apps/tomcat-deployment-datacenter created
service/tomcat-service-datacenter created
```

### Step 2: Verify the Deployment

Check if all resources are created successfully:

```bash
# Verify namespace
kubectl get namespace tomcat-namespace-datacenter

# Verify deployment
kubectl get deployment -n tomcat-namespace-datacenter

# Verify pods
kubectl get pods -n tomcat-namespace-datacenter

# Verify service
kubectl get service -n tomcat-namespace-datacenter
```

### Step 3: Wait for Pod to be Ready

```bash
kubectl wait --for=condition=ready pod -l app=tomcat-datacenter -n tomcat-namespace-datacenter --timeout=300s
```

### Step 4: Check Pod Status

```bash
# Get detailed pod information
kubectl describe pod -l app=tomcat-datacenter -n tomcat-namespace-datacenter

# Check pod logs
kubectl logs -l app=tomcat-datacenter -n tomcat-namespace-datacenter
```

## Accessing the Application

### Method 1: Using NodePort

Get the node IP address:
```bash
kubectl get nodes -o wide
```

Access the application:
```bash
curl http://<NODE_IP>:32227
```

Or open in a browser:
```
http://<NODE_IP>:32227
```

### Method 2: Port Forwarding (for testing)

```bash
kubectl port-forward -n tomcat-namespace-datacenter service/tomcat-service-datacenter 8080:8080
```

Then access locally:
```
http://localhost:8080
```

## Troubleshooting

### Pod is not starting

Check pod events and logs:
```bash
kubectl describe pod -l app=tomcat-datacenter -n tomcat-namespace-datacenter
kubectl logs -l app=tomcat-datacenter -n tomcat-namespace-datacenter
```

### Service is not accessible

Verify service endpoints:
```bash
kubectl get endpoints -n tomcat-namespace-datacenter tomcat-service-datacenter
```

Check if the pod is running:
```bash
kubectl get pods -n tomcat-namespace-datacenter -o wide
```

Verify NodePort service:
```bash
kubectl describe service tomcat-service-datacenter -n tomcat-namespace-datacenter
```

### Image pull issues

Check image pull status:
```bash
kubectl describe pod -l app=tomcat-datacenter -n tomcat-namespace-datacenter | grep -A 5 "Events"
```

## Useful Commands

### View all resources in the namespace
```bash
kubectl get all -n tomcat-namespace-datacenter
```

### Get detailed service information
```bash
kubectl describe service tomcat-service-datacenter -n tomcat-namespace-datacenter
```

### Check deployment rollout status
```bash
kubectl rollout status deployment/tomcat-deployment-datacenter -n tomcat-namespace-datacenter
```

### Scale the deployment (if needed)
```bash
kubectl scale deployment tomcat-deployment-datacenter --replicas=2 -n tomcat-namespace-datacenter
```

### Restart the deployment
```bash
kubectl rollout restart deployment/tomcat-deployment-datacenter -n tomcat-namespace-datacenter
```

## Cleanup

To remove all resources:

```bash
kubectl delete -f tomcat-deployment.yaml
```

Or delete the namespace (which removes all resources within it):

```bash
kubectl delete namespace tomcat-namespace-datacenter
```

## Configuration Details

### Labels
All resources use the label `app: tomcat-datacenter` for consistent identification and selection.

### Networking
- **Container Port**: 8080 (internal)
- **Service Port**: 8080 (cluster-internal)
- **NodePort**: 32227 (external access)

### Resource Isolation
All resources are deployed in the `tomcat-namespace-datacenter` namespace for logical separation from other applications.

## Security Considerations

- The application is exposed via NodePort (32227) on all cluster nodes
- Consider implementing network policies for additional security
- Use appropriate RBAC rules for namespace access control
- Monitor and limit resource usage with ResourceQuotas if needed

## Support

For issues or questions:
1. Check the troubleshooting section
2. Review pod logs and events
3. Contact the DevOps team

## Version Information

- **Kubernetes API Version**: v1
- **Apps API Version**: apps/v1
- **Image**: gcr.io/kodekloud/centos-ssh-enabled:tomcat

---

**Note**: Ensure the application is fully up and running before validation. Use the verification commands provided above to confirm the deployment status.
