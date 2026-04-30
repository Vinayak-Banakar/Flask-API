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

        stage('Build Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%BUILD_NUMBER% -t %IMAGE_NAME%:latest ."
            }
        }

        // 🔍 SIMPLE DEBUG (SAFE)
        stage('DEBUG CREDS') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    powershell '''
                    Write-Output "USER=$env:USER"
                    if ($env:PASS) {
                        Write-Output "PASS RECEIVED"
                    } else {
                        Write-Output "PASS NOT RECEIVED"
                        exit 1
                    }
                    '''
                }
            }
        }

        // 🔐 FINAL LOGIN (ROBUST)
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    powershell '''
                    $pass = $env:PASS
                    echo $pass | docker login -u $env:USER --password-stdin

                    if ($LASTEXITCODE -ne 0) {
                        Write-Error "Docker login failed"
                        exit 1
                    } else {
                        Write-Output "Docker login success"
                    }
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE_NAME%:%BUILD_NUMBER%"
                bat "docker push %IMAGE_NAME%:latest"
            }
        }

        stage('Deploy') {
            steps {
                bat """
                docker stop devops-container || exit 0
                docker rm devops-container || exit 0
                docker run -d -p 5000:5000 --name devops-container %IMAGE_NAME%:%BUILD_NUMBER%
                """
            }
        }
    }
}