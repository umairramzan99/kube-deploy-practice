pipeline {
    agent any

    environment {
        IMAGE_NAME = "visitor-counter-app"
        IMAGE_TAG = "latest"
        DEPLOY_YAML = "deployment.yaml"
        SERVICE_YAML = "service.yaml"
        REDIS_DEPLOY = "redis-deployment.yaml"
        REDIS_SERVICE = "redis-service.yaml"
        REDIS_PV = "redis-pv.yaml"
        REDIS_PVC = "redis-pvc.yaml"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    dir('app') {
                        sh """
                            export DOCKER_TLS_VERIFY=1
                            export DOCKER_HOST=tcp://192.168.58.2:2376
                            export DOCKER_CERT_PATH=/var/lib/jenkins/.minikube/certs
                            export MINIKUBE_ACTIVE_DOCKERD=minikube
                            docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                        """
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    // Deploy Redis components
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_PV}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_PVC}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_DEPLOY}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_SERVICE}"

                    // Deploy Visitor app
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${DEPLOY_YAML}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${SERVICE_YAML}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit using: minikube service visitor-service --url"
        }
        failure {
            echo "❌ Deployment failed. Check pipeline logs."
        }
    }
}
