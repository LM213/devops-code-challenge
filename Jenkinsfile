pipeline {
    agent any

    environment {
        AWS_REGION     = "us-east-1"   // change if needed
        AWS_ACCOUNT_ID = "135808924575"
        FRONTEND_REPO  = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/ecs-challenge-frontend"
        BACKEND_REPO   = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/ecs-challenge-backend"
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
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-credentials']]) {
                    sh """
                    echo "🔎 Checking AWS identity..."
                    aws sts get-caller-identity --region $AWS_REGION

                    echo "🔑 Logging into Amazon ECR..."
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                    """
                }
            }
        }

        stage('Build & Push Backend') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-credentials']]) {
                    sh """
                    cd backend
                    echo "🐳 Building backend image..."
                    docker build -t $BACKEND_REPO:latest .

                    echo "📤 Pushing backend image to ECR..."
                    docker push $BACKEND_REPO:latest
                    """
                }
            }
        }

        stage('Build & Push Frontend') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-credentials']]) {
                    sh """
                    cd frontend
                    echo "🐳 Building frontend image..."
                    docker build -t $FRONTEND_REPO:latest .

                    echo "📤 Pushing frontend image to ECR..."
                    docker push $FRONTEND_REPO:latest
                    """
                }
            }
        }

        stage('Deploy to ECS') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-credentials']]) {
                    sh """
                    echo "🚀 Deploying backend service..."
                    aws ecs update-service \
                        --cluster ecs-challenge-cluster \
                        --service ecs-challenge-backend-service \
                        --force-new-deployment \
                        --region $AWS_REGION

                    echo "🚀 Deploying frontend service..."
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

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs above."
        }
    }
}
