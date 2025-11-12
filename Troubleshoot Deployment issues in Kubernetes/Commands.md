# Redis Deployment Troubleshooting and Fix Commands

# Step 1: Check deployment and pods status
kubectl get deployment redis-deployment
kubectl get pods

# Step 2: Describe the deployment to see the issue
kubectl describe deployment redis-deployment

# Step 3: Get deployment YAML to see configuration
kubectl get deployment redis-deployment -o yaml > redis-deployment-backup.yaml

# Step 4: Check pod details if any exist
kubectl describe pod $(kubectl get pods -l app=redis -o jsonpath='{.items[0].metadata.name}') 2>/dev/null

# Step 5: Fix the deployment - Edit directly
kubectl edit deployment redis-deployment
# Look for common issues:
# - Image name typos (e.g., "rediss" instead of "redis")
# - Wrong port numbers
# - Incorrect volumeMounts or volume names
# - Typos in environment variables

# Step 6: If you identify the issue, you can also patch it directly
# Example fixes:

# If image name is wrong (e.g., "rediss" should be "redis"):
kubectl set image deployment/redis-deployment redis=redis:latest

# If using specific version:
kubectl set image deployment/redis-deployment redis=redis:alpine

# Step 7: Check rollout status
kubectl rollout status deployment redis-deployment

# Step 8: Verify pods are running
kubectl get pods -l app=redis
kubectl get deployment redis-deployment

# Step 9: If still issues, check events
kubectl get events --sort-by=.metadata.creationTimestamp

# Step 10: Test the redis deployment
kubectl get svc | grep redis
# If service exists, test connectivity
