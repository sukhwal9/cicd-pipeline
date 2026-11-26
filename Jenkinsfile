pipeline {
    agent any

    tools {
        nodejs 'Node 7.8.0'
    }

    environment {
        APP_PORT       = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME     = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG      = "v1.0"
        CONTAINER_NAME = "nodeapp-${env.BRANCH_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo "Branch: ${env.BRANCH_NAME} | Port: ${APP_PORT} | Image: ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'CI=true npm test || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    if [ "\$(docker ps -aq -f name=^/${CONTAINER_NAME}\$)" ]; then
                        docker rm -f ${CONTAINER_NAME}
                    fi
                    docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:3000 ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }
}
