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
        script {
              // Dynamically determine the branch to check out
              def branchToBuild = env.BRANCH_NAME ?: 'dev' // Default to 'dev' if BRANCH_NAME is not set
              echo "Checking out branch: ${branchToBuild}"
              // Check out the specified branch
              checkout scmGit(
                branches: [[name: "*/${branchToBuild}"]], 
                extensions: [], 
                userRemoteConfigs: [[url: 'https://github.com/darvin00/capstone_devops-build.git']]
                )
            }
       }
    }
        stage('Build Docker Image') {
            steps {
                script {
                    // Dynamically decide the registry based on the branch
                    def registry = (env.BRANCH_NAME == 'main') ? prodRegistry : devRegistry
                    
                    // Build the Docker image
                    dockerImage = docker.build("${registry}:${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        // Push to the correct registry (dev or prod)
                        dockerImage.push("${env.BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Deploy Application') {
            when {
                branch 'main' // Only deploy when on the main branch
            }
            steps {
                echo "Deploying application from main to production environment..."
                // Add deployment logic for production here
            }
        }
    }
    post {
        always {
            script {
                echo "Cleaning up Docker images..."
                def registry = (env.BRANCH_NAME == 'main') ? prodRegistry : devRegistry
                sh "docker rmi ${registry}:${env.BUILD_NUMBER}"
                sh "docker rmi ${registry}:latest"
            }
        }
    }
}
