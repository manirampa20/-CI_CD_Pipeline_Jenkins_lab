pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials') // DockerHub credentials stored in Jenkins
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
                // Example: For a Maven project (adjust according to your project)
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                // Example: Run tests for Maven project
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    // Build Docker image and tag it with Jenkins build number
                    def appImage = docker.build("your-dockerhub-username/your-app:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Deploy Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_CREDENTIALS) {
                        appImage.push("${env.BUILD_NUMBER}") // Push image with build number tag
                        appImage.push("latest") // Optionally tag it as "latest"
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
