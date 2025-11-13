pipeline {
    agent any

    environment {
        // REPLACE with your Docker Hub username/repository name
        DOCKER_IMAGE = "shubham2209/my-web-app:\${BUILD_ID}"
        // REPLACE with the Credentials ID you will create in Jenkins
        DOCKER_CREDENTIAL_ID = 'secret_text1' 
    }

    stages {
        stage('Build Docker Image') {
            steps {

                    sh "docker build -t shubham2209/my-web-app:${env.BUILD_NUMBER} ."
                  }
            }
        
        stage('Push Image to Registry') {
            steps {
                // Uses the credential ID to login to Docker Hub
                withRegistry('https://registry.hub.docker.com', DOCKER_CREDENTIAL_ID) {
                    sh "docker push \${DOCKER_IMAGE}"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                // Stop and remove any previous container with the same name
                sh "docker stop my-web || true"
                sh "docker rm my-web || true"
                
                // Run the new image, mapping VM port 8081 to container port 80
                sh "docker run -d --name my-web -p 8081:80 \${DOCKER_IMAGE}"
                echo "Deployment complete on port 8081."
            }
        }
    }
}
