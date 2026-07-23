
pipeline {

    agent any

    environment {
        IMAGE_NAME = "nitin1501/website"
    }

    stages {

        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                """
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
                sh """
                docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                docker push ${IMAGE_NAME}:latest
                """
        
        stage('Deploy to Kubernetes') {
    agent {
        label 'k8s-agent'
    }

    steps {
        sh '''
        kubectl get nodes
        kubectl apply -f k8s/deployment.yaml
        kubectl apply -f k8s/service.yaml
        '''
    }
}
            }
        }
    }
}
