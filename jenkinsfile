pipeline {
agent any

```
environment {
    IMAGE_NAME = "infrafox-web"
    CONTAINER_NAME = "infrafox-web"
    PORT = "80"
}

stages {
    stage('Checkout') {
        steps {
            git branch: 'main', url: 'https://github.com/Jaganp-Devops/InfraFox.git'
        }
    }

    stage('Build Docker Image') {
        steps {
            sh 'docker build -t $IMAGE_NAME:${BUILD_NUMBER} .'
        }
    }

    stage('Stop Old Container') {
        steps {
            sh '''
            docker stop $CONTAINER_NAME || true
            docker rm $CONTAINER_NAME || true
            '''
        }
    }

    stage('Run New Container') {
        steps {
            sh '''
            docker run -d \
              --name $CONTAINER_NAME \
              -p 80:80 \
              $IMAGE_NAME:${BUILD_NUMBER}
            '''
        }
    }
}
```
