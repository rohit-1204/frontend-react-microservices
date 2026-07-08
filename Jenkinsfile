pipeline {

    agent { any}
    environment {
        IMAGE_NAME = "rohitpathare1204/react-frontend"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/rohit-1204/frontend-react-microservices.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest
                '''
            }

            
        }

        stage('Docker Hub Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_TOKEN" | docker login \
                        -u "$DOCKER_USER" \
                        --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Logout') {
            steps {
                sh 'docker logout'
            }
        }
    }

    post {
        always {
            sh 'docker image prune -f'
        }
    }
}
