pipeline {
    agent any

    environment {
        CONTAINER_NAME = 'todo-angular-ui'
        IMAGE_NAME = 'todo-frontend'
        NETWORK_NAME = 'todo-network'
        PORT_MAPPING = '4200:80'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build --no-cache -t ${IMAGE_NAME}:latest -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Ensure Docker network exists
                    sh "docker network create ${NETWORK_NAME} || true"
                    
                    // Stop and remove existing container if running
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    
                    // Launch new container
                    sh """
                        docker run -d \
                            --name ${CONTAINER_NAME} \
                            --network ${NETWORK_NAME} \
                            -p ${PORT_MAPPING} \
                            ${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Frontend pipeline completed successfully!"
        }
        failure {
            echo "Frontend pipeline failed. Please check the logs."
        }
    }
}
