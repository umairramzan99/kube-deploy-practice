# Visitor Counter App 🚀

This project is a **Flask + Redis** based web application that counts visitors and stores the count in Redis. It is fully containerized and deployed to **Minikube using Jenkins CI/CD**, and uses a **PersistentVolume** so the visitor count resumes after restarts.

---

## 📁 Project Structure

```
visitor-app/
.
├── app
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
├── deployment.yaml
├── docker-compose.yml
├── Jenkinsfile
├── nginx
│   ├── default.conf
│   └── Dockerfile
├── README.md
├── redis-deployment.yaml
├── redis-pvc.yaml
├── redis-pv.yaml
├── redis-service.yaml
└── service.yaml

```

---

## ⚙️ Prerequisites

- Minikube
- Docker & Docker Hub account
- Jenkins installed (locally or externally)
- Git installed
- Your Docker Hub credentials stored in Jenkins (`dockerhub-creds`)

---

## 🐳 Docker Hub

The visitor app image is built, tagged, and pushed to Docker Hub automatically by Jenkins Pipeline.

Docker Hub Repo: `https://hub.docker.com/repository/docker/umair668/visitor-counter-app`

---

## 🔧 Jenkins Setup

### Required Jenkins Credentials
- **dockerhub-creds**: Type = Username with password
    - Username = `umair668`
    - Password = `********` (securely saved)

### Jenkinsfile Highlights

- Builds and tags Docker image
- Pushes to Docker Hub
- Applies Kubernetes manifests for:
  - `redis-pv.yaml` (Persistent Volume)
  - `redis-pvc.yaml` (Persistent Volume Claim)
  - `redis-deployment.yaml`
  - `redis-service.yaml`
  - `deployment.yaml` (visitor app)
  - `service.yaml` (NodePort)

---

## 🔁 CI/CD Pipeline Flow

1. Clone repo in Jenkins pipeline
2. Build and tag Docker image: `visitor-counter-app:latest`
3. Push image to Docker Hub as `umair668/visitor-counter-app:latest`
4. Apply all Kubernetes manifests
5. Persistent Redis stores visitor count
6. App accessible via: `http://<minikube-ip>:30080`

To get Minikube IP:
```bash
minikube service visitor-service --url
```

---

## 📌 Note

- Make sure Jenkins is connected to the internet to push images.
- Update image names in `deployment.yaml` as needed.

---

## ✅ How to Re-run

```bash
# Re-clone project
git clone https://github.com/umairramzan99/kube-deploy-practice.git
cd kube-deploy-practice

# Push any changes (if needed)
git add .
git commit -m "your message"
git push origin main

# Trigger Jenkins pipeline
# App will be deployed + Redis persistent storage will be used
```

---

## 🧼 Clean Up

```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
kubectl delete -f redis-deployment.yaml
kubectl delete -f redis-service.yaml
kubectl delete -f redis-pv.yaml
kubectl delete -f redis-pvc.yaml
```

---

## 👤 Author

Umair Ramzan
