# Kubernetes Pod Troubleshooting Guide

## Problem Statement

A pod named `webserver` was failing to start in the Kubernetes cluster with `ImagePullBackOff` error, preventing the application from becoming accessible.

## Pod Configuration

- **Pod Name**: `webserver`
- **Namespace**: `default`
- **Containers**:
  - `nginx-container` - Web server (nginx:latest)
  - `sidecar-container` - Log monitoring (ubuntu:latest)
- **Shared Resources**: EmptyDir volume for nginx logs

## Issue Identified

### Root Cause
**Image Name Typo** - The nginx container was configured with an incorrect image tag.

```yaml
# ❌ INCORRECT
image: nginx:latests

# ✅ CORRECT
image: nginx:latest
```

### Error Symptoms
- Pod status: `ImagePullBackOff`
- Ready state: `1/2` (only sidecar running)
- Error message: `failed to pull and unpack image "docker.io/library/nginx:latests": not found`

## Solution

### Step 1: Diagnose the Issue

```bash
# Check pod status
kubectl get pod webserver

# Get detailed error information
kubectl describe pod webserver

# Review events
kubectl get events --sort-by='.lastTimestamp' | grep webserver
```

### Step 2: Delete the Faulty Pod

```bash
kubectl delete pod webserver
```

### Step 3: Create Corrected Manifest

Create a file named `webserver-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver
  labels:
    app: web-app
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/nginx
  
  - name: sidecar-container
    image: ubuntu:latest
    command:
    - sh
    - -c
    - while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log 2>/dev/null || true; sleep 30; done
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/nginx
  
  volumes:
  - name: shared-logs
    emptyDir: {}
```

### Step 4: Apply the Fixed Configuration

```bash
# Apply the corrected manifest
kubectl apply -f webserver-pod.yaml

# Wait for pod to be ready
kubectl wait --for=condition=ready pod/webserver --timeout=60s
```

### Step 5: Verify the Deployment

```bash
# Check pod status (should show Running and 2/2)
kubectl get pod webserver

# Expected output:
# NAME        READY   STATUS    RESTARTS   AGE
# webserver   2/2     Running   0          30s

# Verify nginx is serving content
kubectl exec -it webserver -c nginx-container -- curl localhost

# Check sidecar logs
kubectl logs webserver -c sidecar-container

# Get detailed pod information
kubectl describe pod webserver
```

## Architecture Overview

### Container Roles

**nginx-container**
- Serves web content on port 80
- Writes access and error logs to `/var/log/nginx`
- Primary application container

**sidecar-container**
- Monitors nginx logs from shared volume
- Reads and outputs logs every 30 seconds
- Demonstrates the sidecar pattern for log aggregation

### Shared Storage

- **Volume Type**: EmptyDir (ephemeral storage)
- **Mount Path**: `/var/log/nginx`
- **Purpose**: Share nginx logs between containers

## Testing the Application

### Access nginx Locally

```bash
# Port forward to access from jump_host
kubectl port-forward pod/webserver 8080:80

# In another terminal or browser
curl http://localhost:8080
```

### Expose via Service (Optional)

```bash
# Create a NodePort service
kubectl expose pod webserver --port=80 --target-port=80 --name=webserver-svc --type=NodePort

# Get service details
kubectl get svc webserver-svc

# Access via NodePort
curl http://<NODE_IP>:<NODE_PORT>
```

## Common Kubernetes Troubleshooting Commands

```bash
# Pod status overview
kubectl get pods

# Detailed pod information
kubectl describe pod <pod-name>

# Container logs
kubectl logs <pod-name> -c <container-name>

# Follow logs in real-time
kubectl logs -f <pod-name> -c <container-name>

# Execute commands in container
kubectl exec -it <pod-name> -c <container-name> -- <command>

# View recent events
kubectl get events --sort-by='.lastTimestamp'

# Check pod YAML configuration
kubectl get pod <pod-name> -o yaml
```

## Troubleshooting Tips

### ImagePullBackOff Errors
- Verify image name and tag spelling
- Check image exists in the registry
- Ensure registry is accessible
- Validate image pull secrets (if private registry)

### CrashLoopBackOff Errors
- Check container logs: `kubectl logs <pod> -c <container>`
- Verify application configuration
- Check resource limits
- Review container startup command

### Pending State
- Check node resources: `kubectl describe nodes`
- Verify PVC availability
- Review pod scheduling constraints

## Best Practices

1. **Always use specific image tags** instead of `latest` in production
2. **Implement resource requests and limits** for better scheduling
3. **Use readiness and liveness probes** for health checking
4. **Check events first** when troubleshooting pod issues
5. **Review logs** from all containers in multi-container pods

## Files in This Repository

- `webserver-pod.yaml` - Corrected Kubernetes pod manifest
- `README.md` - This troubleshooting guide

## Environment Details

- **Cluster**: kodekloud-control-plane
- **kubectl**: Pre-configured on jump_host
- **Network**: Pod IP assigned from 10.244.0.0/16

## Status

✅ **Issue Resolved** - Pod is now running with both containers in ready state.

---

**Note**: This guide documents the troubleshooting process for a specific deployment issue. The same diagnostic approach can be applied to similar Kubernetes pod failures.
