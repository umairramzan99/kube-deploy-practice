# 🚀 Visitor Counter App on Kubernetes with CI/CD

This project demonstrates a full CI/CD pipeline for deploying a simple Flask-based visitor counter application on a local Minikube Kubernetes cluster using Jenkins, Docker, and Docker Hub.

---

## 🧱 Tech Stack

- Python (Flask)
- Redis
- Docker & Docker Hub
- Kubernetes (via Minikube)
- Jenkins (with Git SCM Polling)
- Persistent Volume support
- SCM Webhook & Polling Trigger support
- Email Notifications (Build Results)

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

## 🔧 Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/umairramzan99/kube-deploy-practice.git
cd kube-deploy-practice
```

### 2. Start Minikube

```bash
minikube start --driver=docker
```

### 3. Create Required Directories on Host

```bash
mkdir -p /home/umair/redis-data
```

### 4. Apply Kubernetes Configurations

```bash
kubectl apply -f redis-pv.yaml
kubectl apply -f redis-pvc.yaml
kubectl apply -f redis-deployment.yaml
kubectl apply -f redis-service.yaml
```

### 5. Configure Jenkins

- Install required plugins:
  - Docker
  - Docker Pipeline
  - Kubernetes CLI Plugin
  - Git
  - Email Extension

- Add DockerHub credentials in **Jenkins → Manage Jenkins → Credentials** with ID `dockerhub-creds`.

- Configure Jenkins system email:
  - **SMTP server** (e.g. smtp.gmail.com)
  - Default user email suffix and credentials

- Add this job:
  - Select **"Git"** and set repo URL to: `https://github.com/umairramzan99/kube-deploy-practice.git`
  - Under **Build Triggers**, enable:
    - [x] GitHub hook trigger for GITScm polling
    - [x] Poll SCM: `H/2 * * * *`

---

## ⚙️ Jenkinsfile Logic Summary

- Builds Docker image with tag: `visitor-counter-app:<version>` (auto-incremented using Jenkins build number)
- Pushes image to DockerHub as `umair668/visitor-counter-app:<version>`
- Updates `deployment.yaml` to use this image
- Applies `deployment.yaml` and `service.yaml` to Minikube
- Sends email notifications on build success/failure

---

## 🌐 Access the App

After successful deployment:

```bash
minikube service visitor-service --url
```

Example output:
```
http://192.168.49.2:30080
```

---

## 📩 Email Notification Setup

1. Go to `Manage Jenkins → Configure System`
2. In "Extended E-mail Notification":
   - SMTP Server: `smtp.gmail.com`
   - Use SSL: ✅
   - SMTP Port: `465`
   - Sender: `your_email@gmail.com`
   - Credentials: Add Gmail username & App Password

3. Test configuration and save.

In `Jenkinsfile`, add this in `post` block:

```groovy
post {
    success {
        mail to: 'your_email@gmail.com',
             subject: "✅ SUCCESS: Build #${BUILD_NUMBER}",
             body: "Build passed and deployed.
Check DockerHub: ${IMAGE_FULL_NAME}"
    }
    failure {
        mail to: 'your_email@gmail.com',
             subject: "❌ FAILURE: Build #${BUILD_NUMBER}",
             body: "Build failed. Check logs in Jenkins."
    }
}
```

---

## 👤 Author

Umair Ramzan