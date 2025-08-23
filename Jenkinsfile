pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        DOCKER_IMAGE = 'diya0311/bluevaultloanfe'
        KUBERNETES_DEPLOYMENT = 'loan-form-fe'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master', url: 'https://github.com/diyakashyap/loanfrontend.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Generate a dynamic tag based on build number or timestamp
                    env.TAG = "${env.BUILD_NUMBER}" // Or use: `sh('date +%Y%m%d%H%M%S').trim()`
                    
                    // Build the Docker image with the dynamic tag
                    sh "docker build -t $DOCKER_IMAGE:$TAG ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image with the dynamic tag
                    withDockerRegistry([credentialsId: 'dockerhub', url: 'https://index.docker.io/v1/']) {
                        sh "docker push $DOCKER_IMAGE:$TAG"
                    }
                }
            }
        }

        stage('Rolling Update Kubernetes Deployment') {
            steps {
                script {
                    // Update the Kubernetes deployment with the new image
                    sh "kubectl set image deployment/$KUBERNETES_DEPLOYMENT frontend=$DOCKER_IMAGE:$TAG"
                }
            }
        }
    }
}
