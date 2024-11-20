pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'vue-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
        // If using local registry, you might not need DOCKER_REGISTRY
        // DOCKER_REGISTRY = 'your-registry'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build with specific tag
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    // Also tag as latest
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Use the built image in your deployment
                    sh """
                        kubectl apply -f k8s/
                        kubectl set image deployment/vue-app vue-app=${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }
    }

    post {
        always {
            // Clean up
            sh "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG} || true"
            sh "docker rmi ${DOCKER_IMAGE}:latest || true"
        }
    }
}
