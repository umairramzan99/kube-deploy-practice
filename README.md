# Visitor Counter App (Flask + Redis + Kubernetes + Jenkins CI/CD)

This project is a simple visitor counter web app built using Flask and Redis, containerized with Docker, deployed to Kubernetes (Minikube), and managed via Jenkins CI/CD pipeline.

---

## 🧰 Tech Stack

- Python (Flask)
- Redis (with Persistent Volume)
- Docker
- Kubernetes (Minikube)
- Jenkins
- Docker Hub

---

## 🏗️ Project Structure

```
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

## 🚀 Prerequisites

- Docker
- Minikube
- Jenkins running on localhost (e.g., http://localhost:8085)
- GitHub repo for source code
- Docker Hub account

---

## 🔄 CI/CD Pipeline (via Jenkins)

Each push to GitHub triggers the Jenkins pipeline which:
1. Builds the Docker image for the Flask app
2. Tags it as `visitor-counting-app:<build-number>`
3. Pushes it to Docker Hub (`umair669/visitor-counting-app:<build-number>`)
4. Deploys it to Minikube
5. Uses a persistent Redis volume to retain visitor count across pods

### 📝 Jenkinsfile Notes:
- Tags are dynamic based on Jenkins build number.
- Docker login uses credentials stored in Jenkins as `dockerhub-creds`.
- Deployment image is replaced dynamically using `sed`.

---

## 🧪 Running Locally (Manual Steps)

```bash
# Clone the repo
git clone https://github.com/umairramzan99/kube-deploy-practice.git
cd kube-deploy-practice

# Deploy Redis and Volume
kubectl apply -f redis-pv.yaml
kubectl apply -f redis-pvc.yaml
kubectl apply -f redis-deployment.yaml
kubectl apply -f redis-service.yaml

# Replace <build-number> with latest
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Access app
minikube service visitor-service --url
```

---

## 🔐 Docker Hub

Images are automatically pushed to:

📦 https://hub.docker.com/r/umair669/visitor-counting-app

Tags:
- `1.1`, `1.2`, ... based on Jenkins build number

---

## 📂 Persistent Volume

The Redis data is stored at:

```yaml
hostPath:
  path: /home/umair/redis-data
```

Ensure the path exists on your host system.

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


## 🗓️ Last Updated

2025-07-04 10:27:01


## 👤 Author

Umair Ramzan
### SCM Trigger Test - Fri Jul  4 04:27:46 PM PKT 2025
