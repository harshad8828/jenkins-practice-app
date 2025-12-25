pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install & Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t shaikh8828/jenkins-practice-app:${BUILD_NUMBER} .'
                sh 'docker tag shaikh8828/jenkins-practice-app:${BUILD_NUMBER} shaikh8828/jenkins-practice-app:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker push shaikh8828/jenkins-practice-app:${BUILD_NUMBER}"
                    sh "docker push shaikh8828/jenkins-practice-app:latest"
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sh '''
                    docker rm -f my-app || true
                    
                    docker pull shaikh8828/jenkins-practice-app:latest
                    
                    docker run -d --name my-app -p 8081:3000 shaikh8828/jenkins-practice-app:latest
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout'
            echo 'Pipeline finished!'
        }
    }
}
