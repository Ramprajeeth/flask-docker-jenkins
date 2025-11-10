
pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-jenkins-demo"
        CONTAINER_NAME = "flask-jenkins-container"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Run Container') {
            steps {
                echo 'Running container...'
                sh '''
                docker ps -q --filter "name=$CONTAINER_NAME" | grep -q . && docker stop $CONTAINER_NAME && docker rm $CONTAINER_NAME || true
                docker run -d --name $CONTAINER_NAME -p 5000:5000 $IMAGE_NAME
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing application endpoint...'
                sh '''
                sleep 5
                curl -f http://localhost:5000 || (echo "App failed to start" && exit 1)
                '''
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh '''
            docker stop $CONTAINER_NAME || true
            docker rm $CONTAINER_NAME || true
            '''
        }
        success {
            echo 'Pipeline completed successfully ✅'
        }
        failure {
            echo 'Pipeline failed ❌'
        }
    }
}
