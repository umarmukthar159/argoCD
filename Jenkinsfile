pipeline{
    agent any
    environment{
    IMAGE_REPO_NAME="nginx/node-app"
    IMAGE_TAG="latest"
    AWS_REGION="us-east-1"
    ECR_REPO="140023360432.dkr.ecr.us-east-1.amazonaws.com"
    }
    stages{
        stage('checkout SCM'){
            steps{
                checkout scm
            }
        }
        stage('Logging into ECR Repo'){
            steps{
               withCredentials([usernamePassword(credentialsId: '92433954-ed12-47f2-b212-f7040064efa6', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                sh """
                    aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                    aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                    aws configure set region ${AWS_REGION}
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 140023360432.dkr.ecr.us-east-1.amazonaws.com
                """
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
        stage('Push the docker image'){
            steps{
                script{
                sh """
                   docker tag "${IMAGE_REPO_NAME}:${IMAGE_TAG}" "${ECR_REPO}/${IMAGE_REPO_NAME}:${env.BUILD_NUMBER}"
                   docker push "${ECR_REPO}/${IMAGE_REPO_NAME}:${BUILD_NUMBER}"
                """
                }
            }
        }
        stage('Update the helm values'){
            steps{
              withCredentials([gitUsernamePassword(credentialsId: '2025cc27-bcf7-4625-abc3-892b448e3e62', gitToolName: 'Default')]) {
              sh """
              git pull https://github.com/umarmukthar159/argoCD/
              git switch main
              sed -i "s/^tag: .*/tag: \"${BUILD_NUMBER}\"/" node-app-helm/values.yaml
              git config user.email "umarmukthar.n@contus.in"
              git config user.name "umarmukthar159"
              git add node-app-helm/values.yaml
              git commit -m "update values"
              git push
              """
              }
            }
        }
        
    }
}