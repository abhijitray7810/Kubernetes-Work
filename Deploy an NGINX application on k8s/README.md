# Kubernetes NGINX Deployment with Custom HTML

This project demonstrates how to deploy an **NGINX web server** on **Kubernetes** using YAML manifests, including a **custom HTML page** served via a **ConfigMap**. It covers creating a namespace, pod, deployment, service, and ConfigMap with volume mounts.

---
![image](https://github.com/abhijitray7810/Kubernetes-Work/blob/2a08fc327bd315a256d9893c33c29b8da9a616d0/Deploy%20an%20NGINX%20application%20on%20k8s/Screenshot%202025-10-15%20010344.png)

## 🚀 **Features**

* Dedicated Kubernetes namespace (`nginx-namespace`)
* NGINX Pod and Deployment with 2 replicas
* NodePort service to expose NGINX externally
* Custom HTML page served using a ConfigMap
* Fully automated YAML manifests for quick deployment
 
---

## 🗂️ **Project Files**

| File                  | Description                                                                   |
| --------------------- | ----------------------------------------------------------------------------- |
| `nginx-setup.yml`     | Namespace, Pod, Deployment, and Service YAML for NGINX                        |
| `nginx-configmap.yml` | ConfigMap containing custom `index.html` page                                 |
| `deployment.yml`      | Deployment with ConfigMap volume mount (can be combined with nginx-setup.yml) |

---

## ⚡ **Deployment Steps**

1. **Create Namespace, Pod, Deployment, and Service**

```bash
kubectl apply -f nginx-setup.yml
```

2. **Create ConfigMap with custom HTML**

```bash
kubectl apply -f nginx-configmap.yml
```

3. **Update Deployment to mount ConfigMap**

```bash
kubectl apply -f deployment.yml
```

4. **Verify resources**

```bash
kubectl get all -n nginx-namespace
kubectl get configmap -n nginx-namespace
```

5. **Access NGINX**

* If using **Minikube**:

```bash
minikube service nginx-service -n nginx-namespace
```

* If using a Kubernetes node:

```
http://<Node-IP>:30080
```

---

## ✅ **Validation**

* NGINX pods are running:

```bash
kubectl get pods -n nginx-namespace
```

* Service is accessible via NodePort:

```bash
kubectl get svc -n nginx-namespace
```

* Custom HTML page loads correctly in browser or via curl:

```bash
curl http://<Node-IP>:30080
```

---

## 📌 **Notes**

* The default NGINX port `80` is exposed via NodePort `30080`.
* ConfigMap can be easily updated for new HTML content; pods will need a restart to pick up changes.
* This setup is ideal for learning Kubernetes deployments, ConfigMaps, and services.

---
