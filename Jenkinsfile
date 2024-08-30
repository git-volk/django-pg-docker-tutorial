pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "git-volk/django-pg-docker-tutorial"
        DOCKER_TAG = "${env.GIT_TAG_NAME}" // Используем имя тега
        SERVER_IP = "84.201.129.186"
        SERVER_DIR = "/tmp/my_app_chart"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push("${DOCKER_TAG}")
                    }
                }
            }
        }

        stage('Update values.yaml on Server') {
            steps {
                script {
                    sshagent(['ssh-credentials-id']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no user@${SERVER_IP} 'sed -i "s/tag:.*/tag: ${DOCKER_TAG}/" ${SERVER_DIR}/values.yaml'
                        """
                    }
                }
            }
        }

        stage('Helm Upgrade') {
            steps {
                script {
                    sshagent(['ssh-credentials-id']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no user@${SERVER_IP} 'cd ${SERVER_DIR} && helm upgrade --install my-app-release .'
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
