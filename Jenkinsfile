pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'jarvis0912'
        IMAGE_NAME     = 'devops-capstone-app'
        IMAGE_TAG      = "${BUILD_NUMBER}"
        APP_SERVER_IP  = '13.203.76.176'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} .
                    docker tag ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                        docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to App Server') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${APP_SERVER_IP} << 'EOF'
                            # Pull the latest image
                            docker pull ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                            
                            # Stop and remove existing container if running
                            docker stop web-app || true
                            docker rm web-app || true
                            
                            # Run new container with persistent log volume
                            docker run -d \
                              --name web-app \
                              --restart unless-stopped \
                              -p 3000:3000 \
                              -v /var/log/nodeapp:/usr/src/app/logs \
                              ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                              
                            # Clean unused dangling images
                            docker image prune -f
EOF
                    """
                }
            }
        }
    }

    post {
        always {
            sh 'docker image prune -f'
        }
        success {
            echo 'Pipeline executed and application deployed successfully.'
        }
        failure {
            echo 'Pipeline execution failed.'
        }
    }
}
