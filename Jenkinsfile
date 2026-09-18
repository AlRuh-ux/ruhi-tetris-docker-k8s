pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = '555044956356.dkr.ecr.us-east-1.amazonaws.com/my-tetris-game'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
    }

    stages {
        stage('Source') {
            steps {
                git branch: 'main', url: 'https://github.com/AlRuh-ux/ruhi-tetris-docker-k8s.git'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t $ECR_REPO:$IMAGE_TAG .'
                sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO'
                sh 'docker push $ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Test') {
            steps {
                sh 'docker rm -f tetris-test-container || true'
                sh 'docker run -d --name tetris-test-container $ECR_REPO:$IMAGE_TAG'
                sh 'sleep 5'
                sh 'docker exec tetris-test-container wget -q -O- http://localhost:80 | grep -q "Tetris" && echo "Test passed"'
                sh 'docker stop tetris-test-container && docker rm tetris-test-container'
            }
        }

        stage('Deploy') {
            steps {
                sh 'helm upgrade my-tetris-game ./helm/my-tetris-game --namespace my-tetris --create-namespace --set image.tag=$IMAGE_TAG'
            }
        }
    }
}