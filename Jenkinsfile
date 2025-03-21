pipeline {
    agent any

    environment {
        registry = "registry.gitlab.com/devops9033903/devops"
        registryCredential = 'gitlab-credentials-id' // Jenkins credential ID for GitLab registry
        dockerImage = ''
        k8sConfigPath = '/home/ubuntu/ritesh' // Path to Kubernetes manifests
        vmHost = '13.208.182.172' // Replace with your VM's IP address
        sshKey = 'ssh-key' // Jenkins credential ID for SSH key
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    dockerImage = docker.build("${registry}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image to GitLab Registry') {
            steps {
                script {
                    // Push the image to GitLab registry
                    docker.withRegistry('https://registry.gitlab.com', registryCredential) {
                        dockerImage.push()
                        dockerImage.push("latest") // Push both unique and latest tags
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sshagent([sshKey]) {
                    script {
                        // Deploy the application to Minikube
                        sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${vmHost} 
                        kubectl delete deployment gitlab-app --cascade=true --ignore-not-found
                        kubectl apply -f ${k8sConfigPath}/app.yaml
                        kubectl set image deployment/gitlab-app gitlab-container=${registry}:${env.BUILD_NUMBER}
                        kubectl rollout restart deployment gitlab-app
                        
                        """
                    }
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
