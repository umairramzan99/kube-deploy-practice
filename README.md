
# Visitor Counter App with Redis, Kubernetes & Jenkins CI/CD

This project is a simple Flask + Redis web app that shows the number of visitors. It uses:

- ✅ Flask app as the frontend (`app/app.py`)
- 🧠 Redis for storing visitor count
- ☸️ Kubernetes for deployment (Minikube)
- 📦 PersistentVolume so visitor count is saved across pod restarts
- ⚙️ Jenkins pipeline for automated CI/CD
- 🐳 Docker to containerize the app

---

## 📁 Directory Structure

```
.
├── app
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
├── deployment.yaml
├── service.yaml
├── Jenkinsfile
├── redis-deployment.yaml
├── redis-service.yaml
├── redis-pv.yaml
├── redis-pvc.yaml
```

---

## 🚀 Getting Started

### 🔧 Prerequisites

- Minikube installed and started
- Jenkins running locally (e.g. at `localhost:8085`)
- Docker installed
- Git installed

---

## 🛠️ Setup Instructions

### 1. Clone the Repo

```bash
git clone https://github.com/umairramzan99/kube-deploy-practice.git
cd kube-deploy-practice
```

### 2. Ensure Redis Volume Directory Exists

```bash
mkdir -p /home/umair/redis-data
```

This path will be used as the host path for Redis data.

---

### 3. Start Minikube with Volume Mount

```bash
minikube stop
minikube start --mount --mount-string="/home/umair/redis-data:/mnt/redis-data"
```

---

### 4. Set Up Jenkins Access to Minikube

> If Jenkins fails to reach Minikube, do this:

```bash
# As host user
sudo mkdir -p /var/lib/jenkins/.kube /var/lib/jenkins/.minikube
sudo cp -r ~/.kube/config /var/lib/jenkins/.kube/config
sudo cp -r ~/.minikube/profiles ~/.minikube/certs /var/lib/jenkins/.minikube/
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube /var/lib/jenkins/.minikube
```

Edit `/var/lib/jenkins/.kube/config` and replace any path starting with `/home/umair/.minikube` to `/var/lib/jenkins/.minikube`

---

### 5. Create Jenkins Pipeline

In Jenkins UI:

- Go to **New Item**
- Choose **Pipeline**
- Use this Git repo: `https://github.com/umairramzan99/kube-deploy-practice.git`
- Pipeline script path: `Jenkinsfile`
- Click **Build Now**

---

## 🌐 Access the App

```bash
minikube service visitor-service --url
```

It should open something like:

```
http://192.168.xx.xx:30080
```

---

## 🧪 Test Persistence

- Visit the app → Refresh 4–5 times → note the visitor count
- Run:  
  ```bash
  kubectl delete pod -l app=visitor
  ```
- Wait for pod to restart
- Visit the URL again

✅ Visitor number should continue (e.g., 6, 7…)  
❌ If it resets to 1, check if `/home/umair/redis-data/` contains `dump.rdb`

---

## 📤 Pushing to Docker Hub (Optional)

Once ready to push images to Docker Hub:

```bash
docker login
docker tag visitor-counter-app umairhub/visitor-counter-app:latest
docker push umairhub/visitor-counter-app:latest
```

Update `deployment.yaml` to pull from Docker Hub.

---

## 📃 Credits

Created by [Umair Ramzan](https://github.com/umairramzan99)
