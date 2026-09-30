pipeline {
    agent any

    environment {
        BACKEND_IMAGE = 'hruthik16ks/employee_backend:v1'
        FRONTEND_IMAGE = 'hruthik16ks/employee_frontend:v1'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hruthik16ks/project1.git'
            }
        }

        stage('Verify Docker Images') {
            steps {
                bat 'docker images %BACKEND_IMAGE%'
                bat 'docker images %FRONTEND_IMAGE%'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Pull Docker Images') {
            steps {
                bat 'docker pull %BACKEND_IMAGE%'
                bat 'docker pull %FRONTEND_IMAGE%'
            }
        }

        stage('Deploy Backend') {
            steps {
                bat '''
                    docker network create employee-network || exit 0
                    docker network connect employee-network ems-mysql || exit 0
                    docker rm -f backend || exit 0
                    docker run -d --name backend --network employee-network -p 8082:8082 %BACKEND_IMAGE%
                '''
            }
        }

        stage('Deploy Frontend') {
            steps {
                bat '''
                    docker rm -f frontend || exit 0
                    docker run -d --name frontend -p 3001:80 %FRONTEND_IMAGE%
                '''
            }
        }

        stage('Verify') {
            steps {
                bat 'docker ps'
            }
        }
    }
}
