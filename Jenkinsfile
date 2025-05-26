pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'docker-hub-creds'
        IMAGE_NAME = 'faizan2203/todo-application:latest'
        GIT_REPO = 'https://github.com/Faizan-shariff/todo-application.git'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${GIT_REPO}", branch: 'master', credentialsId: 'Github-creds'  // Use credentials if private repo
            }
        }
        stage('Build with Maven') {
            steps {
                // Using maven container or assume maven is installed on agent
                bat 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${IMAGE_NAME} ."
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    bat '''
                        echo $PASSWORD | docker login -u $USERNAME --password-stdin
                        docker push ${IMAGE_NAME}
                        docker logout
                    '''
                }
            }
        }
        stage('Deploy with Docker Compose') {
            steps {
                bat 'docker compose down || exit /B 0'
                bat 'docker compose up -d'
            }
        }
        stage('Clean Workspace') {
            steps {
                bat 'del /q /f *.* & for /d %%x in (*) do @rmdir /s /q "%%x"'
            }
        }
    }
}
