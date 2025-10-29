## 🔍 Step-by-Step Commands

### 1️⃣ Check Pod Status

```bash
kubectl get pods
```

### 2️⃣ Describe the Pod for More Details

```bash
kubectl describe pod webserver
```

### 3️⃣ Check Logs of Each Container

```bash
kubectl logs webserver -c nginx-container
kubectl logs webserver -c sidecar-container
```

---

## 🧰 Fix the Issue

### 4️⃣ Edit the Pod Configuration

```bash
kubectl edit pod webserver
```

Modify the **`sidecar-container`** section to include a sleep command to keep it running:

```yaml
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
  - name: sidecar-container
    image: ubuntu:latest
    command: ["sleep", "infinity"]
```

Save and exit the editor.

---

## ✅ Verify the Fix

### 5️⃣ Confirm Pod is Running

```bash
kubectl get pods
```

Expected output:

```
webserver   2/2   Running   0   <age>
```

### 6️⃣ Verify Application Accessibility

If exposed through a service:

```bash
kubectl get svc
```

Then access:

```bash
curl <ClusterIP>:<Port>
# or if NodePort
curl <NodeIP>:<NodePort>
```

---

## 🏁 Result

✅ The **`webserver`** pod is now in a **Running** state.
✅ Both containers (`nginx-container` and `sidecar-container`) are active.
✅ The Nginx application is accessible successfully.

---

