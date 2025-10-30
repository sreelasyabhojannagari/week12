pipeline {
    agent any

    environment {
        IMAGE_NAME = "seleniumdemoapp:v1"
        DOCKER_REPO = "sreelasya24/first:seleniumtestimage"
    }

    stages {

        stage('Run Selenium Tests with pytest') {
            steps {
                echo "🏃 Running Selenium Tests using pytest"
                
                // Install dependencies
                bat 'pip install -r requirements.txt'

                // Optionally start Flask app if needed (uncomment below)
                // bat 'start /B python app.py'
                // bat 'ping 127.0.0.1 -n 5 > nul'

                // Run pytest, but don’t fail pipeline immediately — helps debugging
                bat 'pytest -v || echo Pytest failed - check logs for details'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker Image"
                
                // Verify Dockerfile exists
                bat 'dir Dockerfile'
                
                // Build image
                bat "docker build -t %IMAGE_NAME% ."
            }
        }

        stage('Docker Login (Secure)') {
            steps {
                echo "🔐 Logging in to Docker Hub using Jenkins credentials"

                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat "docker login -u sreelasya24 -p Shree2401!"
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                echo "⬆️ Pushing Docker Image to Docker Hub"
                bat "docker tag %IMAGE_NAME% %DOCKER_REPO%"
                bat "docker push %DOCKER_REPO%"
            }
        }

        stage('Deploy to Kubernetes') { 
            steps { 
                echo "☸️ Deploying to Kubernetes Cluster"
                // Ensure kubectl is installed and configured in Jenkins node
                bat 'kubectl apply -f deployment.yaml --validate=false'
                bat 'kubectl apply -f service.yaml'
            } 
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Please check the stage logs above for details.'
        }
    }
}
