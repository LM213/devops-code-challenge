pipeline {
    agent any

    environment {
        AWS_REGION    = "us-east-1"   // change if different
        AWS_ACCOUNT_ID = "135808924575"
        FRONTEND_REPO = "${AWS_ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/ecs-challenge-frontend"
        BACKEND_REPO  = "${AWS_ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/ecs-challenge-backend"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/LM213/devops-code-challenge.git'
            }
        }

        stage('Login to ECR') {
            steps {
                script {
                    sh """
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                    """
                }
            }
        }

        stage('Build & Push Backend') {
            steps {
                script {
                    sh """
                    cd backend
                    docker build -t $BACKEND_REPO:latest .
                    docker push $BACKEND_REPO:latest
                    """
                }
            }
        }

        stage('Build & Push Frontend') {
            steps {
                script {
                    sh """
                    cd frontend
                    docker build -t $FRONTEND_REPO:latest .
                    docker push $FRONTEND_REPO:latest
                    """
                }
            }
        }

        stage('Deploy to ECS') {
            steps {
                script {
                    sh """
                    aws ecs update-service \
                        --cluster ecs-challenge-cluster \
                        --service ecs-challenge-backend-service \
                        --force-new-deployment \
                        --region $AWS_REGION

                    aws ecs update-service \
                        --cluster ecs-challenge-cluster \
                        --service ecs-challenge-frontend-service \
                        --force-new-deployment \
                        --region $AWS_REGION
                    """
                }
            }
        }
    }
}
