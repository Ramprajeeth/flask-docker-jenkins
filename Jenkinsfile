
pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = '457606182356.dkr.ecr.ap-south-1.amazonaws.com/flask-jenkins-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $ECR_REPO:latest .'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                    sh '''
                        aws ecr get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin $ECR_REPO
                    '''
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                    docker push $ECR_REPO:latest
                '''
            }
        }

        stage('Clean up local images') {
            steps {
                sh '''
                    docker rmi $ECR_REPO:latest || true
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Build and Push completed successfully!"
        }
        failure {
            echo "❌ Build failed. Check logs for errors."
        }
    }
}
