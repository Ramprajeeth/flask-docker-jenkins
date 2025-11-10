
pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '123456789012'
        ECR_REPO = 'flask-jenkins-demo'
        IMAGE_TAG = "latest"
        IMAGE_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_URI .'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION \
                | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                docker push $IMAGE_URI
                '''
            }
        }

        stage('Clean up local images') {
            steps {
                sh '''
                docker rmi $IMAGE_URI || true
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Successfully pushed image to ECR: $IMAGE_URI"
        }
        failure {
            echo "❌ Build failed. Check logs for errors."
        }
    }
}
