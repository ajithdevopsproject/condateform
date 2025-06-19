pipeline {
    agent any

    environment {
        BACKEND_IMAGE = 'ajithdocgym/contactform-backend:latest'
        FRONTEND_IMAGE = 'ajithdocgym/contactform-frontend:latest'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // You must create this in Jenkins > Credentials
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📥 Cloning source code..."
                git branch: 'main', url: 'https://github.com/ajithdevopsproject/condateform.git'
            }
        }

        stage('Clean Docker Environment') {
            steps {
                echo "🧹 Cleaning previous Docker setup..."
                sh 'docker compose down --remove-orphans || true'
                sh 'docker image prune -af || true'
            }
        }

        stage('Build Backend Image') {
            steps {
                echo "🔧 Building backend image..."
                sh '''
                    docker build -t contactform-backend:latest ./backend
                    docker tag contactform-backend:latest $BACKEND_IMAGE
                '''
            }
        }

        stage('Build Frontend Image') {
            steps {
                echo "🔧 Building frontend image..."
                sh '''
                    docker build -t contactform-frontend:latest ./frontend
                    docker tag contactform-frontend:latest $FRONTEND_IMAGE
                '''
            }
        }

        stage('Login to DockerHub') {
            steps {
                echo "🔐 Logging into DockerHub..."
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                echo "📤 Pushing images..."
                sh "docker push $BACKEND_IMAGE"
                sh "docker push $FRONTEND_IMAGE"
            }
        }

        stage('Deploy App') {
            steps {
                echo "🚀 Deploying application using Docker Compose..."
                sh 'docker compose up -d --build'
            }
        }
    }

    post {
        success {
            echo '✅ Deployment pipeline completed successfully!'
        }
        failure {
            echo '❌ Deployment pipeline failed. Check logs.'
        }
    }
}
