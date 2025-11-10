
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



// Send email after pipeline completes
currentBuild.result = currentBuild.result ?: 'SUCCESS'

if (currentBuild.result == 'SUCCESS') {
    emailext (
        to: 'your_email@gmail.com',
        subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
        body: """<p>Build <b>${env.JOB_NAME} #${env.BUILD_NUMBER}</b> succeeded!</p>
                 <p><a href='${env.BUILD_URL}'>View Build Logs</a></p>""",
        mimeType: 'text/html'
    )
} else {
    emailext (
        to: 'your_email@gmail.com',
        subject: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
        body: """<p>Build <b>${env.JOB_NAME} #${env.BUILD_NUMBER}</b> failed.</p>
                 <p><a href='${env.BUILD_URL}'>View Build Logs</a></p>""",
        mimeType: 'text/html'
    )
}

