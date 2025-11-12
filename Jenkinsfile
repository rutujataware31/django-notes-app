pipeline {
    agent any

    environment {
        IMAGE_NAME = "notes-app"
        IMAGE_TAG = "latest"
        DOCKERHUB_CREDENTIALS = "dockerHubCreds"
        DOCKERHUB_USER = "rutuja3112"
    }

    stages {
        stage('Clone Code') {
            steps {
                echo "Cloning repository..."
                git branch: 'main', url: 'https://github.com/rutujataware31/django-notes-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    bat "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    echo "Pushing image to DockerHub..."
                    withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        bat """
                            echo %PASSWORD% | docker login -u %USERNAME% --password-stdin
                            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                            docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    echo "Running container locally..."
                    bat "docker run -d -p 8000:8000 ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}
