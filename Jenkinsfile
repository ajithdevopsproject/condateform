pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // optional
    }

    triggers {
        githubPush() // Optional: triggers on push from GitHub webhook
    }

    stages {
        stage('Test') {
            steps {
                echo '✅ Jenkinsfile is working!'
            }
        }

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ajithdevopsproject/condateform.git'
            }
        }

        stage('Clean Docker') {
            steps {
                script {
                    echo "Stopping and removing old containers..."
                    sh 'docker-compose down --remove-orphans || true'

                    echo "Pruning unused Docker images..."
                    sh 'docker image prune -af || true'
                }
            }
        }

        stage('Build Backend') {
            steps {
                script {
                    docker.build('contactform-backend:latest', './backend')
                }
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    docker.build('contactform-frontend:latest', './frontend')
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker-compose up -d --build'
            }
        }
    }
}
