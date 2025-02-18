pipeline{
    agent any
    environment{
    IMAGE_REPO_NAME=nginx/node-app
    IMAGE_TAG=latest
    }
    stages{
        stage('checkout SCM'){
            steps{
                checkout scm
            }
        }
        stage('Logging into ECR Repo'){
            steps{
                script{
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 140023360432.dkr.ecr.us-east-1.amazonaws.com"
                }
            }
        }
        stage('Build the docker image'){
            steps{
                script{
                    app = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}