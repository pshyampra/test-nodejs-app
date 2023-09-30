pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Git checkout the repository
                git branch: 'main', url: 'https://github.com/pshyampra/test-nodejs-app.git'
            }
        }

        stage('Build and Publish Docker Image') {
            steps {
                script {
                    // Step 2: Build Docker image and publish to ECR
                    def ecrRepoUrl = "485517222763.dkr.ecr.ap-south-1.amazonaws.com"
                    def awsRegion = "ap-south-1"
                    def ecrLogin = sh(script: "aws ecr get-login-password --region ${awsRegion} | /usr/bin/docker login --username AWS --password-stdin ${ecrRepoUrl}",returnStatus: true)

                    if (ecrLogin == 0) {
                        sh "docker build -t app ."
                        sh "docker tag app:latest ${ecrRepoUrl}/app:latest"
                        sh "docker push ${ecrRepoUrl}/app:latest"
                    } else {
                        error "Failed to authenticate with ECR"
                    }
                }
            }
        }
       stage('Deploy to App Host') {
            steps {
                script {

                        sh "docker pull ${ecrRepoUrl}/app:latest"
                        sh "docker stop my-node-app-container || true"
                        sh "docker rm my-node-app-container || true"
                        sh "docker run -d --name my-node-app-container -p 8080:8081 ${ecrRepoUrl}/app:latest"

                    }
                }
    }
 }
}
