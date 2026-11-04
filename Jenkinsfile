pipeline {
    agent any

    environment {
        APP_NAME = 'simplybyte-calculator'
        PORT = '5000'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image ${APP_NAME}:latest..."
                    if (isUnix()) {
                        sh "docker build -t ${APP_NAME}:latest ."
                    } else {
                        bat "docker build -t ${APP_NAME}:latest ."
                    }
                }
            }
        }

        stage('Stop Existing Container') {
            steps {
                script {
                    echo "Stopping and removing existing container if running..."
                    if (isUnix()) {
                        sh "docker stop ${APP_NAME} || true"
                        sh "docker rm ${APP_NAME} || true"
                    } else {
                        bat "docker stop ${APP_NAME} 2>nul || verify >nul"
                        bat "docker rm ${APP_NAME} 2>nul || verify >nul"
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    echo "Starting new container on port ${PORT}..."
                    if (isUnix()) {
                        sh "docker run -d --name ${APP_NAME} -p ${PORT}:${PORT} ${APP_NAME}:latest"
                    } else {
                        bat "docker run -d --name ${APP_NAME} -p ${PORT}:${PORT} ${APP_NAME}:latest"
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo "Checking container status..."
                    if (isUnix()) {
                        sh "docker ps --filter \"name=${APP_NAME}\""
                    } else {
                        bat "docker ps --filter \"name=${APP_NAME}\""
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Successfully deployed ${APP_NAME}! Access it at http://<server-ip>:${PORT}"
        }
        failure {
            echo "Deployment failed. Check the console output above for details."
        }
    }
}
