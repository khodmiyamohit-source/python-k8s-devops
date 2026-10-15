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
                bat 'docker tag python-k8s-devops:%BUILD_NUMBER% mohitdevops74/python-k8s-devops:latest'

                bat 'docker push mohitdevops74/python-k8s-devops:%BUILD_NUMBER%'
                bat 'docker push mohitdevops74/python-k8s-devops:latest'
            }
        }
        

        stage('Kubernetes Check') {
            steps {
                withEnv(['KUBECONFIG=C:\\Users\\MOHIT\\.kube\\config']) {
                bat 'kubectl config current-context'
                bat 'kubectl get nodes'
        }
    }
}

        stage('Kubernetes Deploy') {
            steps {
                withEnv(['KUBECONFIG=C:\\Users\\MOHIT\\.kube\\config']) {
                bat 'kubectl apply -f k8s/deployment.yaml'
                bat 'kubectl apply -f k8s/service.yaml'
                bat 'kubectl rollout status deployment/python-k8s-devops --timeout=120s'
        }
    }
}


    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the Console Output for the failing stage.'
        }
    }
}
