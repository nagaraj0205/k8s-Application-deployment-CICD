pipeline {
    agent any

    environment {
        IMAGE_NAME = "nagarajan02/nginx-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Clone') {
    steps {
        git branch: 'main',
            url: 'https://github.com/nagaraj0205/k8s-Application-deployment-CICD.git'
    }
}

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                kubectl set image deployment/nginx-demo \
                nginx-demo=$IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
