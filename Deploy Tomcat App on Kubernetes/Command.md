# Step 1: Create the namespace
kubectl create namespace tomcat-namespace-datacenter

# Step 2: Create the deployment
kubectl create deployment tomcat-deployment-datacenter \
  --image=gcr.io/kodekloud/centos-ssh-enabled:tomcat \
  --namespace=tomcat-namespace-datacenter

# Step 3: Update the deployment to set proper container name and port
kubectl patch deployment tomcat-deployment-datacenter \
  -n tomcat-namespace-datacenter \
  -p '{
    "spec": {
      "replicas": 1,
      "template": {
        "spec": {
          "containers": [{
            "name": "tomcat-container-datacenter",
            "image": "gcr.io/kodekloud/centos-ssh-enabled:tomcat",
            "ports": [{"containerPort": 8080}]
          }]
        }
      }
    }
  }'

# Step 4: Expose the deployment with a NodePort service
kubectl expose deployment tomcat-deployment-datacenter \
  --type=NodePort \
  --name=tomcat-service-datacenter \
  --port=8080 \
  --target-port=8080 \
  --namespace=tomcat-namespace-datacenter

# Step 5: Patch the service to set the nodePort to 32227
kubectl patch service tomcat-service-datacenter \
  -n tomcat-namespace-datacenter \
  -p '{
    "spec": {
      "ports": [{
        "port": 8080,
        "targetPort": 8080,
        "nodePort": 32227
      }]
    }
  }'

# Step 6: Verify everything
kubectl get all -n tomcat-namespace-datacenter
