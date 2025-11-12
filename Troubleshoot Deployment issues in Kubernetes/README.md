# Redis Deployment Fix on Kubernetes

## 📘 Overview
The Nautilus DevOps team deployed a Redis application on the Kubernetes cluster. The application was initially running fine, but after a recent configuration change, the pods went down.  
This document outlines the root cause and the steps taken to fix the issue.

---

## 🧩 Problem Description
Upon inspecting the Redis deployment using:
```bash
kubectl get deploy redis-deployment -o yaml
````

Two key issues were found:

1. **Incorrect image name**

   ```yaml
   image: redis:alpin
   ```

   The correct image name should be `redis:alpine`.

2. **Incorrect ConfigMap reference**

   ```yaml
   name: redis-cofig
   ```

   The correct ConfigMap name should be `redis-config`.

These errors caused the Redis pods to fail during initialization.

---

## 🛠️ Fix Implementation

### 1. Corrected the Redis image name

```bash
kubectl set image deployment/redis-deployment redis-container=redis:alpine
```

### 2. Corrected the ConfigMap reference

Verified existing ConfigMaps:

```bash
kubectl get configmaps
```

Then patched the deployment to use the correct ConfigMap:

```bash
kubectl patch deployment redis-deployment --type='json' \
  -p='[{"op": "replace", "path": "/spec/template/spec/volumes/1/configMap/name", "value":"redis-config"}]'
```

### 3. Restarted the deployment

```bash
kubectl rollout restart deployment redis-deployment
kubectl rollout status deployment redis-deployment
```

---

## ✅ Verification

Checked that the Redis pod is up and running:

```bash
kubectl get pods
```

Expected output:

```
NAME                                 READY   STATUS    RESTARTS   AGE
redis-deployment-xxxxx               1/1     Running   0          1m
```

---

## 🧾 Summary of Fix Commands

```bash
kubectl set image deployment/redis-deployment redis-container=redis:alpine
kubectl patch deployment redis-deployment --type='json' -p='[{"op": "replace", "path": "/spec/template/spec/volumes/1/configMap/name", "value":"redis-config"}]'
kubectl rollout restart deployment redis-deployment
kubectl get pods
```

---

## 📄 Conclusion

The Redis deployment failure was caused by a typo in the image name (`redis:alpin`) and a misnamed ConfigMap reference (`redis-cofig`).
After correcting these and redeploying, the Redis pods came back to a **Running** state successfully.

---
