pipeline {
    agent any

    stages {

        stage('Build Image') {
            steps {
                bat 'docker build -t vinayak0910/devops-app:22 .'
            }
        }

        stage('Push Image') {
            steps {
                bat 'docker push vinayak0910/devops-app:22'
            }
        }

    }
}