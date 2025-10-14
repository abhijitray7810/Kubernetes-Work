# Verify & Show Output
## Check all resources:
```
kubectl get all -n nginx-namespace
```
## Access the app (on your browser or curl):
```
curl http://<Node-IP>:30080

```
## using minikube, use:
```
minikube service nginx-service -n nginx-namespace
```
