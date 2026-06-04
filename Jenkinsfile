pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-2"
        ECR_REPO = "211425018449.dkr.ecr.ap-south-2.amazonaws.com/nginx-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $ECR_REPO:$IMAGE_TAG ."
            }
        }

        stage('Push to ECR') {
            steps {
                sh """
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ECR_REPO
                docker push $ECR_REPO:$IMAGE_TAG
                """
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh """
                aws eks update-kubeconfig --region $AWS_REGION --name nginx-cluster
                kubectl set image deployment/nginx-deployment nginx=$ECR_REPO:$IMAGE_TAG
                """
            }
        }
    }
}
