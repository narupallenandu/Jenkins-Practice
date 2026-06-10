pipeline {
    agent any

    environment {
        IMAGE_NAME = "narupallenandu/jenkin"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Greeting') {
            steps {
                echo 'Hello Nandu from Jenkins!'
            }
        }

        stage('Checkout Code') {
            steps {
                git 'https://github.com/narupallenandu/Jenkins-Practice.git'
            }
        }

        stage('Verify Docker') {
            steps {
                sh 'docker --version'
                sh 'docker ps'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG Dockerfile/'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('Success') {
            steps {
                echo 'Docker image pushed successfully to Docker Hub!'
            }
        }
    }
}
