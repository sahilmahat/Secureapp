pipeline {
    agent any

    environment {
        IMAGE_NAME = "secure-ci-cd-app"
        CONTAINER_NAME = "secure-ci-cd-container"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh '''
                  docker run --rm $IMAGE_NAME pytest
                '''
            }
        }

        stage('Trivy Security Scan') {
            steps {
                sh '''
                  trivy image --exit-code 0 --severity LOW,MEDIUM $IMAGE_NAME
                  trivy image --exit-code 1 --severity HIGH,CRITICAL $IMAGE_NAME
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                  docker stop $CONTAINER_NAME || true
                  docker rm $CONTAINER_NAME || true
                  docker run -d --name $CONTAINER_NAME -p 5000:5000 $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}

