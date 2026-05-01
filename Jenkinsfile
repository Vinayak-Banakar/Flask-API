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

        // ✅ NEW: TEST STAGE
        stage('Test') {
            steps {
                bat "pip install -r requirements.txt"
                bat "pytest"
            }
        }

        stage('Build Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%BUILD_NUMBER% -t %IMAGE_NAME%:latest ."
            }
        }

        // 🔐 CLEAN LOGIN (NO DEBUG)
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    powershell '''
                    $env:PASS | docker login -u $env:USER --password-stdin
                    if ($LASTEXITCODE -ne 0) { exit 1 }
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

        // ✅ VERSION LOGGING
        stage('Deploy') {
            steps {
                echo "Deploying version %BUILD_NUMBER%"
                bat """
                docker stop devops-container || exit 0
                docker rm devops-container || exit 0
                docker run -d -p 5000:5000 --name devops-container %IMAGE_NAME%:%BUILD_NUMBER%
                """
            }
        }

        // ✅ CLEANUP (OPTIONAL BUT GOOD)
        stage('Cleanup') {
            steps {
                bat "docker system prune -f"
            }
        }
    }
}