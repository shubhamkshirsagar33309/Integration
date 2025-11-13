pipeline {
    agent any

    environment {
        // Defines the base image name (repository/image_name)
        // Note: Using DOCKER_IMAGE_TAG is cleaner than referencing env.BUILD_NUMBER repeatedly
        DOCKER_IMAGE_BASE = "shubham2209/my-web-app"
        
        // Defines the specific tag for this build (e.g., :10, :11, etc.)
        DOCKER_IMAGE_TAG = "${env.BUILD_NUMBER}" 
        
        // Defines the full image name used by Docker commands
        DOCKER_FULL_IMAGE = "${DOCKER_IMAGE_BASE}:${DOCKER_IMAGE_TAG}"

        // Credential ID for Docker Hub (This is the critical fix!)
        DOCKER_CREDENTIAL_ID = 'secret_text2' 
    }

    stages {
        stage('Build Docker Image') {
            steps {
                // Use the defined variable DOCKER_FULL_IMAGE for consistency
                sh "docker build -t ${DOCKER_FULL_IMAGE} ."
            }
        }

        stage('Push Image to Registry') {
            steps {
                // Now uses the correct credential ID: 'secret_text2'
                withDockerRegistry(credentialsId: env.DOCKER_CREDENTIAL_ID, url: 'https://registry.hub.docker.com') {
                    // Push the image using the full tag
                    sh "docker push ${DOCKER_FULL_IMAGE}"
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Stopping and removing previous container named 'my-web'..."
                // Stop and remove any previous container with the same name
                sh "docker stop my-web || true"
                sh "docker rm my-web || true"

                echo "Deploying ${DOCKER_FULL_IMAGE} on port 8081."
                // Run the new image, mapping VM port 8081 to container port 80
                // Use the consistent DOCKER_FULL_IMAGE variable
                sh "docker run -d --name my-web -p 8081:80 ${DOCKER_FULL_IMAGE}"
                
                echo "Deployment complete. Application available on port 8081."
            }
        }
    }
}
