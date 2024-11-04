pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from your Git repository
                git 'https://github.com/Grunt-prog/flask-app-cicd.git' // Change to your GitHub repository URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using the Dockerfile in the repository
                    sh "docker build -t flask-app ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Remove any existing container with the same name (optional)
                    sh "docker rm -f flask-app || true"

                    // Run the Docker container
                    sh "docker run -d --name flask-app -p 5000:5000 flask-app"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}