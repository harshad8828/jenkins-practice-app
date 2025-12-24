pipeline {
    agent any

    environment {
        DOCKER_USER = "shaikh8828"
        IMAGE_NAME  = "jenkins-practice-app"
        IMAGE_TAG   = "${env.BUILD_ID}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/harshad8828/jenkins-practice-app.git'
            }
        }

        stage('Install & Test') {
            steps {
                bat 'npm install'
                bat 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG% ."
                bat "docker tag %DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG% %DOCKER_USER%/%IMAGE_NAME%:latest"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    bat "echo %PASS%| docker login -u %USER% --password-stdin"
                    bat "docker push %DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG%"
                    bat "docker push %DOCKER_USER%/%IMAGE_NAME%:latest"
                }
            }
        }
    }

    post {
        always {
            bat "docker logout"
            echo "Pipeline finished!"
        }
        success {
            echo "Build %IMAGE_TAG% pushed successfully to Docker Hub!"
        }
        failure {
            echo "Build failed! Please check the console log below."
        }
    }
}
