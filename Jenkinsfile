pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = credentials('dockerhub-username')
        DOCKERHUB_PASSWORD = credentials('dockerhub-password')
        SSH_KEY = credentials('vm-ssh-key')
        SSH_USER = "ubuntu"
        SSH_HOST = "YOUR_VM_PUBLIC_IP"
        BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/mean-backend:latest"
        FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/mean-frontend:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/YOUR_GITHUB_USERNAME/crud-dd-task-mean-app.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                script {
                    sh """
                        cd backend
                        docker build -t ${BACKEND_IMAGE} .
                    """
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                script {
                    sh """
                        cd frontend
                        docker build -t ${FRONTEND_IMAGE} .
                    """
                }
            }
        }

        stage('Login to Docker Hub & Push Images') {
            steps {
                script {
                    sh '''
                        echo "$DOCKERHUB_PASSWORD" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                        docker push ${BACKEND_IMAGE}
                        docker push ${FRONTEND_IMAGE}
                    '''
                }
            }
        }

        stage('Deploy on Remote Server') {
            steps {
                script {
                    writeFile file: 'deploy.sh', text: """
                        #!/bin/bash
                        cd /opt/mean-app
                        git pull
                        docker compose pull
                        docker compose up -d
                    """
                    sh 'chmod +x deploy.sh'

                    // Copy deployment script to VM
                    sh """
                        scp -o StrictHostKeyChecking=no -i $SSH_KEY deploy.sh $SSH_USER@$SSH_HOST:/home/$SSH_USER/
                    """

                    // Execute the deployment
                    sh """
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY $SSH_USER@$SSH_HOST 'bash /home/$SSH_USER/deploy.sh'
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}
