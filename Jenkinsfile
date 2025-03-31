pipeline {
    agent any

    environment {
        IMAGE_NAME = 'crypto-app'
        DOCKER_REGISTRY = 'your-docker-username'
        DOCKER_CREDENTIALS_ID = 'docker-credentials'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Sudhansu-12/Crypto-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    docker.image("${DOCKER_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}").inside {
                        sh 'npm install && npm test'  // Modify as needed
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                script {
                    sh """
                    docker stop crypto-app || true
                    docker rm crypto-app || true
                    docker run -d --name crypto-app -p 3000:3000 ${DOCKER_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
