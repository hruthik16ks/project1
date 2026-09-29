pipeline {
agent any

```
environment {
    DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
    DOCKERHUB_USERNAME = 'hruthik16ks'
    BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/employee_backend:v1"
    FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/employee_frontend:v1"
}

stages {

    stage('Checkout') {
        steps {
            git branch: 'main',
                url: 'https://github.com/hruthik16ks/project1.git'
        }
    }

    stage('Build Backend') {
        steps {
            dir('ems-backend') {
                bat 'mvn clean package -DskipTests'
            }
        }
    }

    stage('Build Frontend') {
        steps {
            dir('ems-frontend') {
                bat 'npm.cmd install'
                bat 'npm.cmd run build'
            }
        }
    }

    stage('Docker Build') {
        steps {
            bat 'docker build -t %BACKEND_IMAGE% ./ems-backend'
            bat 'docker build -t %FRONTEND_IMAGE% ./ems-frontend'
        }
    }

    stage('Docker Login & Push') {
        steps {
            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )
            ]) {
                bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                bat 'docker push %BACKEND_IMAGE%'
                bat 'docker push %FRONTEND_IMAGE%'
            }
        }
    }

    stage('Deploy') {
        steps {
            bat '''
                docker network create employee-network || exit 0
                docker network connect employee-network ems-mysql || exit 0
                docker rm -f backend || exit 0
                docker rm -f frontend || exit 0
                docker run -d --name backend --network employee-network -p 8082:8082 %BACKEND_IMAGE%
                docker run -d --name frontend -p 3001:80 %FRONTEND_IMAGE%
            '''
        }
    }
}
```

}
