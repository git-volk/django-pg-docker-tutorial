pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "git-volk/django-pg-docker-tutorial"
        DOCKER_TAG = "${env.GIT_TAG_NAME}" // Используем имя тега
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
    }

    post {
        always {
            cleanWs()
        }
    }
}
