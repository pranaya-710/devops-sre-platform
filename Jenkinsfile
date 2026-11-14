
pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = 'pranaya710/devops-sre'
    }

    stages {

        stage('Verify Tools') {
            steps {
                bat 'git --version'
                bat 'docker --version'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKERHUB_REPO%:%BUILD_NUMBER% .'
                bat 'docker tag %DOCKERHUB_REPO%:%BUILD_NUMBER% %DOCKERHUB_REPO%:latest'
            }
        }

        stage('Health Check') {
            steps {
                script {
                    def containerName = "devops-test-${env.BUILD_NUMBER}"

                    try {
                        bat "docker run -d --name ${containerName} ${env.DOCKERHUB_REPO}:${env.BUILD_NUMBER}"

                        bat 'python -c "import time; time.sleep(3)"'

                        bat """
                        docker exec ${containerName} python -c "import urllib.request; r=urllib.request.urlopen('http://127.0.0.1:5000/health'); print(r.read().decode()); assert r.status == 200"
                        """
                    }
                    finally {
                        bat(
                            script: "docker rm -f ${containerName}",
                            returnStatus: true
                        )
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-devops',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        bat 'echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin'
                        bat 'docker push %DOCKERHUB_REPO%:%BUILD_NUMBER%'
                        bat 'docker push %DOCKERHUB_REPO%:latest'
                    }
                }
            }
        }
    }
}
