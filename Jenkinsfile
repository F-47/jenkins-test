pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    environment {
        IMAGE_NAME = "executioner47/python-iti"
    }
    stages {
        stage('Build') {
            steps {
                script {
                    echo "Build Number: ${currentBuild.number}"
                    if (currentBuild.number < 5) {
                        error("❌ Build number is less than 5. Stopping pipeline!")
                    }
                }
                sh "mvn clean package"
            }
        }
        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKERHUB_PASS')]) {
                    sh "echo $DOCKERHUB_PASS | docker login -u executioner47 --password-stdin"
                }
            }
        }
        stage('Docker Push') {
            steps {
                sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }
        stage('Deploy') {
            steps {
                sh """
                echo "🚀 Deploying container..."

                # Stop & remove old container if exists
                docker stop python-app || true
                docker rm python-app || true

                # Run new container on port 9000 instead of 8080
                docker run -d -p 9000:8080 --name python-app ${IMAGE_NAME}:${BUILD_NUMBER}

                echo "✅ App is running on: http://<your-server-ip>:9000"
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
        always {
            echo "🧹 Cleaning workspace..."
            cleanWs()
        }
    }
}