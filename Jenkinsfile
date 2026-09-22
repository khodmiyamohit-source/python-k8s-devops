pipeline {
    agent any

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/khodmiyamohit-source/python-k8s-devops.git'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t python-k8s-devops:%BUILD_NUMBER% .'
            }
        }

        stage('DockerHub Push') {
            steps {
                bat 'docker tag python-k8s-devops:%BUILD_NUMBER% mohitdevops74/python-k8s-devops:%BUILD_NUMBER%'
                bat 'docker push mohitdevops74/python-k8s-devops:%BUILD_NUMBER%'
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                bat 'kubectl apply -f k8s/deployment.yaml'
                bat 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}