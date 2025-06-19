pipeline {
    agent any

    environment {
        BACKEND_IMAGE = 'ajithdocgym/contactform-backend:latest'
        FRONTEND_IMAGE = 'ajithdocgym/contactform-frontend:latest'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // Make sure this ID matches Jenkins credentials
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📦 Cloning GitHub repository"
                git branch: 'main', url: 'https://github.com/ajithdevopsproject/condateform.git'
            }
        }

        stage('Clean Docker') {
            steps {
                echo "🧹 Cleaning Docker"
                sh 'docker-compose down --remove-orphans || true'
                sh 'docker image prune -af || true'
            }
        }

        stage('Build Backend Image') {
            steps {
                echo "🐳 Building Backend Docker Image"
                sh """
                    docker build -t contactform-backend:latest ./backend
                    docker tag contactform-backend:latest $BACKEND_IMAGE
                """
            }
        }

        stage('Build Frontend Image') {
            steps {
                echo "🐳 Building Frontend Docker Image"
                sh """
                    docker build -t contactform-frontend:latest ./frontend
                    docker tag contactform-frontend:latest $FRONTEND_IMAGE
                """
            }
        }

        stage('DockerHub Login') {
            steps {
                echo "🔐 Logging into DockerHub"
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                echo "🚀 Pushing Images to DockerHub"
                sh "docker push $BACKEND_IMAGE"
                sh "docker push $FRONTEND_IMAGE"
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                echo "🚢 Deploying Application Stack"
                sh 'docker-compose up -d --build'
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
