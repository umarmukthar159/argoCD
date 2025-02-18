pipeline{
    agent any

    stages{
        stage('Build the Docker image'){
            app = docker.build("node/latest")
        }
    }
}