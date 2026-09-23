pipeline {
    agent any

    environment {
        IMAGE_NAME = "jenkins-cicd-demo"
        CONTAINER_NAME = "jenkins-cicd-app"
        HOST_PORT = "8081"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                    echo "Building application..."
                    test -f index.html
                    test -f Dockerfile
                    echo "Build successful"
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    echo "Running tests..."
                    grep -q "<html" index.html
                    echo "Tests passed"
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    echo "Building Docker image..."
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                    docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Docker Run/Deploy') {
            steps {
                sh '''
                    echo "Deploying application..."

                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${HOST_PORT}:80 \
                        ${IMAGE_NAME}:${BUILD_NUMBER}

                    sleep 5

                    echo "Testing deployed application..."
                    curl -f http://localhost:${HOST_PORT}

                    echo "Application deployed successfully!"
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed!'
        }
    }
}