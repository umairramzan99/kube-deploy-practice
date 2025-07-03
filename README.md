# Visitor Counter App with CI/CD Pipeline (Jenkins + Kubernetes)

This project is a simple Python Flask app that counts visitors using Redis, containerized with Docker, and deployed via Kubernetes using a Jenkins CI/CD pipeline running on Minikube.

---

## 🛠 Tech Stack

- Python + Flask
- Redis
- Docker
- Kubernetes (via Minikube)
- Jenkins (external, running on localhost:8085)

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

## 🚀 How to Run

### 1. Clone the Repository

```bash
git clone https://github.com/umairramzan99/kube-deploy-practice.git
cd kube-deploy-practice
```

---

### 2. Set Up Minikube (if not already running)

Start your Minikube cluster:

```bash
minikube start
```

Ensure Docker inside Minikube is configured:

```bash
eval $(minikube docker-env)
```

---

### 3. Set Up Jenkins

Make sure Jenkins is:
- Installed and accessible at `http://localhost:8085`
- Has the following plugins:
  - Git
  - Pipeline
- Has access to your `.kube/config` file (`/var/lib/jenkins/.kube/config`)

---

### 4. Create Jenkins Pipeline

1. Open Jenkins
2. Create a **New Item** → **Pipeline**
3. Choose **"Pipeline script from SCM"**
4. Use the GitHub repo URL:
   ```
   https://github.com/umairramzan99/kube-deploy-practice.git
   ```
5. Jenkins will use the `Jenkinsfile` in this repo to:
   - Build Docker image
   - Deploy Redis and Flask app to Minikube

---

### 5. Access the App

After a successful pipeline run, open:

```bash
minikube service visitor-service --url
```

You should see:

```
Hello from Flask! You are visitor number: 1
```

---

## 🧼 Cleanup

To delete everything:

```bash
kubectl delete deployment visitor-app redis
kubectl delete service visitor-service redis
```

---

## 🙌 Author

**Umair Ramzan**  
A hands-on DevOps & Kubernetes practice project.  
Feel free to fork and build on top of it!
