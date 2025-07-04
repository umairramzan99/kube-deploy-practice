pipeline {
    agent any

    environment {
        IMAGE_NAME = "visitor-counter-app"
        VERSION_PREFIX = "1"
        IMAGE_TAG = "${VERSION_PREFIX}.${BUILD_NUMBER}"
        DOCKERHUB_USERNAME = "umair668"
        IMAGE_FULL_NAME = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
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
                dir('app') {
                    sh """
                        docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_FULL_NAME}
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds', 
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_FULL_NAME}
                    """
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    // Update app deployment image to Docker Hub image
                    sh "sed -i 's|image:.*|image: ${IMAGE_FULL_NAME}|' ${DEPLOY_YAML}"

                    // Create PV and PVC before deploying Redis
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_PV}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_PVC}"

                    // Deploy Redis
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_DEPLOY}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${REDIS_SERVICE}"

                    // Deploy visitor app
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${DEPLOY_YAML}"
                    sh "KUBECONFIG=${KUBECONFIG} kubectl apply -f ${SERVICE_YAML}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Image pushed and everything deployed successfully from Docker Hub!"
        }
        failure {
            echo "❌ Something went wrong. Check pipeline logs."
        }
    }
}
