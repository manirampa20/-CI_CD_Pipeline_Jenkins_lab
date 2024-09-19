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
                    docker.build("manirampa20/dockerdemo-app:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    try {
                        echo 'Pushing Docker image to Docker Hub...'
                        bat '''
                            echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR  --password-stdin
                            docker push manirampa20/dockerdemo-app:${env.BUILD_NUMBER}
                            docker-compose down
                            docker-compose pull
                            docker-compose up -d
                            docker logout
                        '''
                        echo 'Deployment completed successfully'
                    } catch (Exception e) {
                        error "Deployment failed: ${e.message}"
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
