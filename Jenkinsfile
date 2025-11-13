pipeline {
    agent any

    environment {
        // REPLACE with your Docker Hub username/repository name
        DOCKER_IMAGE = "shubham2209/my-web-app:\${BUILD_ID}"
        // REPLACE with the Credentials ID you will create in Jenkins
        DOCKER_CREDENTIAL_ID = 'secret_text3' 
    }

    stages {
        stage('Build Docker Image') {
            steps {

                    sh "docker build -t shubham2209/my-web-app:${env.BUILD_NUMBER} ."
                  }
            }
        
        stage('Push Image to Registry') {
            steps {
                // Authenticate using the specific Jenkins Credential ID
                // Note: 'DOCKER_CREDENTIAL_ID' must be defined somewhere else (like an 'environment' block or parameter)
                withDockerRegistry(credentialsId: 'secret_text3', url: 'https://registry.hub.docker.com') {
            
                    // Push the image using the exact tag from the build stage
                    sh "docker push shubham2209/my-web-app:${env.BUILD_NUMBER}"
            
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
