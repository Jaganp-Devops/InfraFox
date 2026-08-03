pipeline {
    agent any

    environment {
        IMAGE_NAME = "infrafox-web"
        CONTAINER_NAME = "infrafox-web"
        PORT = "80"
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout Source') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image..."
                sh """
                docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                """
            }
        }

        stage('Stop Old Container') {
            steps {
                echo "Stopping Old Container..."
                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                """
            }
        }

        stage('Remove Old Image') {
            steps {
                echo "Removing Old Docker Image..."
                sh """
                docker image rm ${IMAGE_NAME}:latest || true
                """
            }
        }

        stage('Tag Image') {
            steps {
                sh """
                docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Run Container') {
            steps {
                echo "Starting New Container..."
                sh """
                docker run -d \
                --name ${CONTAINER_NAME} \
                -p ${PORT}:80 \
                --restart unless-stopped \
                ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Docker Cleanup') {
            steps {
                echo "Cleaning Docker..."

                sh """
                docker image prune -f
                docker container prune -f
                docker builder prune -f
                """
            }
        }

        stage('Deployment Verification') {
            steps {
                sh """
                docker ps
                """
            }
        }

    }

    post {

        success {
            echo "===================================="
            echo " Deployment Successful 🚀"
            echo " GitHub → Jenkins → Docker Completed"
            echo "===================================="
        }

        failure {
            echo "===================================="
            echo " Deployment Failed ❌"
            echo "===================================="
        }

        always {
            cleanWs()
        }
    }
}