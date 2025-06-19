pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // Add this credential in Jenkins
        BACKEND_IMAGE = 'ajithdocgym/contactform-backend:latest'
        FRONTEND_IMAGE = 'ajithdocgym/contactform-frontend:latest'
    }

    triggers {
        githubPush() // Trigger build on GitHub push
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
                    sh "docker tag contactform-backend:latest $BACKEND_IMAGE"
                }
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    docker.build('contactform-frontend:latest', './frontend')
                    sh "docker tag contactform-frontend:latest $FRONTEND_IMAGE"
                }
            }
        }

        stage('Docker Hub Login') {
            steps {
                echo "🔐 Logging into Docker Hub"
                sh """
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                """
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                echo "🚀 Pushing images to Docker Hub"
                sh "docker push $BACKEND_IMAGE"
                sh "docker push $FRONTEND_IMAGE"
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker-compose up -d --build'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
    }
}
