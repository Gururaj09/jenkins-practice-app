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
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    // This 'tool' wrapper automatically pulls the scanner path into the shell
                    script {
                        def scannerHome = tool 'SonarQubeScanner'
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=jenkins-practice \
                            -Dsonar.projectName="Jenkins Practice App" \
                            -Dsonar.sources=src/main/java \
                            -Dsonar.java.binaries=target/classes
                        """
                    }
                }
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
                script {
                    // Use Groovy double quotes so ${env.BUILD_NUMBER} evaluates properly right here
                    sh """
                        echo "Starting practice container..."
                        
                        # Stop and remove old container if it exists
                        docker stop practice-app-container || true
                        docker rm practice-app-container || true
                        
                        # Run the new container using the correct build number tag
                        docker run -d --name practice-app-container practice-app:${env.BUILD_NUMBER}
                    """
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
