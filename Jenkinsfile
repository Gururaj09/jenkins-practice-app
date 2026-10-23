pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t practice-app:${env.BUILD_NUMBER} ."
                }
            }
        }

        stage('Run Container Locally') {
            steps {
                sh '''
                    echo "Starting practice container..."
                    docker ps -q --filter "name=practice-app-container" | grep -q . && docker stop practice-app-container && docker rm practice-app-container || true
                    docker run -d --name practice-app-container practice-app:${env.BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        success {
            echo 'Practice pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}
