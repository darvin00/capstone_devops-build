pipeline {
    agent any
    environment {
        devRegistry = "xave01/dev"
        prodRegistry = "xave01/prod"
        registryCredential = 'DockerHub'
        dockerImage = ''
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/dev']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/darvin00/capstone_devops-build.git']])
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    dockerImage = docker.build("${env.BRANCH_NAME == 'main' ? prodRegistry : devRegistry}:${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        // Push to the appropriate registry (dev or prod)
                        dockerImage.push("${env.BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Deploy Application') {
            steps {
                echo "Deploying application from main to production environment..."
                // Add your deployment logic here
            }
        }
    }
     post {
         always {
            echo "Cleaning up Docker images..."
            sh "docker rmi ${env.BRANCH_NAME == 'main' ? prodRegistry : devRegistry}:${env.BUILD_NUMBER}"
            sh "docker rmi ${env.BRANCH_NAME == 'main' ? prodRegistry : devRegistry}:latest"
        }
    }
}
