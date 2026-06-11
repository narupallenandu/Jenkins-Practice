pipeline {
    agent any

    environment {
        IMAGE_NAME = "narupallenandu/hello-nandu"
        IMAGE_TAG  = "latest"
    }

    stages {

        stage('Greeting') {
            steps {
                echo 'Hello Nandu from Jenkins!'
            }
        }

        stage('Check Environment') {
            steps {
                sh 'whoami'
                sh 'pwd'
                sh 'date'
            }
        }

        stage('Verify Tools') {
            steps {
                sh 'docker --version'
                sh 'syft version'
                sh 'grype version'
            }
        }

        stage('Create Dockerfile') {
            steps {
                writeFile file: 'Dockerfile', text: '''
FROM alpine:latest

CMD ["echo", "Hello Nandu from Docker"]
'''
            }
        }

        stage('Display Dockerfile') {
            steps {
                sh 'cat Dockerfile'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('List Docker Images') {
            steps {
                sh 'docker images'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run --rm ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }

        stage('Generate SBOM with Syft') {
            steps {
                sh 'syft ${IMAGE_NAME}:${IMAGE_TAG} -o table'
            }
        }

        stage('Scan Image with Grype') {
            steps {
                sh 'grype ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'file-docker',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login \
                    -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true'
            }
        }

        stage('Success') {
            steps {
                echo 'Docker Image Built, Scanned and Pushed Successfully!'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
