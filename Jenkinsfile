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
                    // Use standard double quotes for string interpolation in Groovy
                    sh "docker build -t practice-app:${env.BUILD_NUMBER} ."
                }
            }
        }

        stage('Run Container Locally') {
            steps {
                // Pass the build number safely into the shell environment
                withEnv(['BUILD_NUM=${env.BUILD_NUMBER}']) {
                    sh '''
                        echo "Starting practice container..."
                        
                        # Stop and remove old container if it exists
                        docker stop practice-app-container || true
                        docker rm practice-app-container || true
                        
                        # Run the new container using the build number tag
                        docker run -d --name practice-app-container practice-app:${BUILD_NUM}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Practice pipeline completed successfully and container is running!'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}
