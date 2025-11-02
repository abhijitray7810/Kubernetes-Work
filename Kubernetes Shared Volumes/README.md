# Kubernetes Shared Volume Pod - Volume Share Xfusion

## Overview

This project demonstrates how to share volumes between multiple containers within a single Kubernetes pod. The implementation creates a pod with two Debian containers that share an `emptyDir` volume, allowing data written by one container to be accessible by the other.

## Architecture

```
Pod: volume-share-xfusion
│
├── Container 1: volume-container-xfusion-1
│   ├── Image: debian:latest
│   ├── Command: sleep 3600
│   └── Volume Mount: /tmp/ecommerce → volume-share
│
├── Container 2: volume-container-xfusion-2
│   ├── Image: debian:latest
│   ├── Command: sleep 3600
│   └── Volume Mount: /tmp/apps → volume-share
│
└── Volume: volume-share (emptyDir)
```

## Prerequisites

- Kubernetes cluster up and running
- `kubectl` CLI tool installed and configured
- Access to the jump_host with proper cluster credentials

## Files

- `volume-share-xfusion.yaml` - Kubernetes pod manifest with shared volume configuration

## Deployment Instructions

### Step 1: Create the Pod

Apply the YAML manifest to create the pod:

```bash
kubectl apply -f volume-share-xfusion.yaml
```

Expected output:
```
pod/volume-share-xfusion created
```

### Step 2: Verify Pod Status

Check if the pod is running with both containers ready:

```bash
kubectl get pods volume-share-xfusion
```

Expected output:
```
NAME                   READY   STATUS    RESTARTS   AGE
volume-share-xfusion   2/2     Running   0          10s
```

You can also get detailed information:

```bash
kubectl describe pod volume-share-xfusion
```

### Step 3: Test Volume Sharing - Create File in Container 1

Execute into the first container:

```bash
kubectl exec -it volume-share-xfusion -c volume-container-xfusion-1 -- /bin/bash
```

Inside the container, create a test file:

```bash
# Create the test file
echo "This is ecommerce data shared between containers" > /tmp/ecommerce/ecommerce.txt

# Verify the file was created
cat /tmp/ecommerce/ecommerce.txt

# List the directory
ls -la /tmp/ecommerce/

# Exit the container
exit
```

### Step 4: Verify File Exists in Container 2

Execute into the second container:

```bash
kubectl exec -it volume-share-xfusion -c volume-container-xfusion-2 -- /bin/bash
```

Inside the container, verify the shared file:

```bash
# List the directory - you should see ecommerce.txt
ls -la /tmp/apps/

# Read the file content
cat /tmp/apps/ecommerce.txt

# Exit the container
exit
```

### Step 5: Additional Testing (Optional)

You can test bi-directional sharing by creating a file in container 2 and reading it from container 1:

```bash
# In container 2
kubectl exec -it volume-share-xfusion -c volume-container-xfusion-2 -- sh -c "echo 'Data from container 2' > /tmp/apps/test-from-container2.txt"

# Verify in container 1
kubectl exec volume-share-xfusion -c volume-container-xfusion-1 -- cat /tmp/ecommerce/test-from-container2.txt
```

## Volume Type: emptyDir

The `emptyDir` volume type used in this configuration:

- **Lifecycle**: Created when the pod is assigned to a node, exists as long as the pod runs
- **Data Persistence**: Data is deleted permanently when the pod is removed
- **Use Cases**: 
  - Temporary scratch space
  - Checkpointing long computations
  - Holding files fetched by one container for processing by another
  - Sharing data between sidecar containers

## Key Concepts Demonstrated

1. **Multi-Container Pod**: Running multiple containers within a single pod
2. **Volume Sharing**: Using a shared volume for inter-container communication
3. **Volume Mounts**: Mounting the same volume at different paths in different containers
4. **Container Exec**: Accessing containers for testing and verification

## Use Cases

This pattern is useful for:

- **Sidecar Pattern**: Log collection, monitoring agents, or proxies
- **Adapter Pattern**: Transforming data format for the main container
- **Ambassador Pattern**: Proxying connections to external services
- **Data Exchange**: Temporary data sharing between tightly coupled containers

## Troubleshooting

### Pod Not Starting

Check pod events:
```bash
kubectl describe pod volume-share-xfusion
kubectl logs volume-share-xfusion -c volume-container-xfusion-1
```

### Cannot Exec into Container

Ensure the pod is in `Running` state:
```bash
kubectl get pod volume-share-xfusion
```

### File Not Visible in Second Container

Verify volume mounts:
```bash
kubectl describe pod volume-share-xfusion | grep -A 5 "Mounts:"
```

## Cleanup

To remove the pod and associated resources:

```bash
kubectl delete pod volume-share-xfusion
```

Or delete using the manifest:

```bash
kubectl delete -f volume-share-xfusion.yaml
```

## Best Practices

1. **Use Appropriate Volume Types**: Choose `emptyDir` for temporary data, `persistentVolumeClaim` for persistent data
2. **Resource Limits**: Add resource requests and limits in production environments
3. **Readiness Probes**: Implement health checks for production workloads
4. **Security Context**: Set appropriate security contexts and user permissions
5. **Namespace Usage**: Deploy pods in appropriate namespaces for better organization

## Additional Resources

- [Kubernetes Volumes Documentation](https://kubernetes.io/docs/concepts/storage/volumes/)
- [Pod Multi-Container Patterns](https://kubernetes.io/docs/concepts/workloads/pods/#using-pods)
- [EmptyDir Volume Type](https://kubernetes.io/docs/concepts/storage/volumes/#emptydir)

## License

This is a demonstration project for the Nautilus DevOps team.

## Support

For issues or questions, please contact the Nautilus DevOps team.
