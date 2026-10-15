
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        timestamps()
    }

    environment {
        DOCKER_IMAGE = 'mohitdevops74/python-k8s-devops'
        KUBECONFIG = 'C:\\Users\\MOHIT\\.kube\\config'
        KUBECTL = 'C:\\Users\\MOHIT\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\kubectl.exe'
    }

    stages {
        stage('Checkout GitHub') {
            steps {
                checkout scm
                echo 'GitHub source code downloaded successfully.'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker version'
                bat 'docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% -t %DOCKER_IMAGE%:latest .'
            }
        }

        stage('DockerHub Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat '@echo off\r\necho %DOCKER_PASSWORD%| docker login -u "%DOCKER_USERNAME%" --password-stdin'
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                bat 'docker push %DOCKER_IMAGE%:%BUILD_NUMBER%'
                bat 'docker push %DOCKER_IMAGE%:latest'
            }
        }

        stage('Kubernetes Check') {
            steps {
                bat 'if not exist "%KUBECONFIG%" (echo ERROR: Kubernetes config file not found & exit /b 1)'
                bat '"%KUBECTL%" config current-context'
                bat '"%KUBECTL%" get nodes'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '"%KUBECTL%" apply -f k8s/deployment.yaml'
                bat '"%KUBECTL%" apply -f k8s/service.yaml'
                bat '"%KUBECTL%" rollout status -f k8s/deployment.yaml --timeout=120s'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '"%KUBECTL%" get deployments'
                bat '"%KUBECTL%" get pods'
                bat '"%KUBECTL%" get services'
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Docker image pushed and Kubernetes deployment completed.'
        }
        failure {
            echo 'FAILED: Check the first failed stage in the Jenkins console.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
