pipeline {
    agent any

    environment {
        IMAGE_NAME = "vinayak0910/devops-app"
    }

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/Vinayak-Banakar/Flask-API.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
            }
        }

        stage('Run Container') {
            steps {
                bat '''
                docker stop devops-container || exit 0
                docker rm devops-container || exit 0
                docker run -d -p 5000:5000 --name devops-container %IMAGE_NAME%:%BUILD_NUMBER%
                '''
            }
        }
    }
}