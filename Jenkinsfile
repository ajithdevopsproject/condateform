pipeline {
    agent any

    environment {
        BACKEND_IMAGE = 'ajithdocgym/contactform-backend:latest'
        FRONTEND_IMAGE = 'ajithdocgym/contactform-frontend:latest'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ajithdevopsproject/condateform.git'
            }
        }

        stage('Check Docker Compose') {
            steps {
                sh 'which docker'
                sh 'docker compose version'
            }
        }

        stage('Clean Docker') {
            steps {
                sh 'docker compose down --remove-orphans || true'
                sh 'docker image prune -af || true'
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                    docker build -t contactform-backend:latest ./backend
                    docker tag contactform-backend:latest $BACKEND_IMAGE
                '''
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh '''
                    docker build -t contactform-frontend:latest ./frontend
                    docker tag contactform-frontend:latest $FRONTEND_IMAGE
                '''
            }
        }

        stage('DockerHub Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                sh 'docker push $BACKEND_IMAGE'
                sh 'docker push $FRONTEND_IMAGE'
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker compose up -d --build'
            }
        }
    }

    post {
        success {
            echo '✅ Deployment pipeline completed successfully!'
        }
        failure {
            echo '❌ Deployment pipeline failed!'
        }
    }
}
