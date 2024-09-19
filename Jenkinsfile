pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials') // Use the credentials ID from Jenkins
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Cloning repository...'
                git branch: 'main', url: 'https://github.com/manirampa20/CI_CD_Pipeline_Jenkins_lab.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application...'
                bat 'mvn clean package'  // Build the project
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                bat 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    // Build Docker image and tag it with the Jenkins build number
                    def appImage = docker.build("manirampa20/dockerdemo-app:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Deploy Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    try {
                    // Authenticate and push to Docker Hub using the credentials
                        docker.withRegistry('https://registry.hub.docker.com', DOCKER_CREDENTIALS) {
                            appImage.push("${env.BUILD_NUMBER}") // Push the image with the build number tag
                            appImage.push("latest") // Optionally tag it as "latest"
                            } catch (Exception e) {
                            error "Deployment Failed: ${e.message}"
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'Pipeline succeeded.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
