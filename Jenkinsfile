pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ACCOUNT_ID = "010296564465"
        ECR_REPO = "flask-app"
        IMAGE = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                docker tag flask-app:latest $IMAGE
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push $IMAGE
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker pull $IMAGE

                docker stop flask-container || true

                docker rm flask-container || true

                docker run -d \
                  --name flask-container \
                  -p 5000:5000 \
                  $IMAGE
                '''
            }
        }
}
