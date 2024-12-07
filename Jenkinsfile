pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t my-static-website .'
            }
        }
        stage('Run Container') {
            steps {
                sh '''
                docker run -d -p 8081:80 --name my-static-website my-static-website
                '''

            }
        }
        stage('Test') {
            steps {
                sh 'curl -I localhost:8081'
            }
        }
        stage('Tag and Push Image') {
            steps {
                script {
                    // Use credentials to log in to Docker Hub
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        // Log in to Docker Hub
                        sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"

                        // Tag the image with the correct namespace and repo name
                        def buildId = env.BUILD_ID ?: "local"
                        sh 'docker tag my-static-website docker.io/shivanipa/my-static-website:latest'
                        sh "docker tag my-static-website docker.io/shivanipa/my-static-website:develop-${buildId}"

                        // Push the image to Docker Hub (repository will be auto-created)
                        sh 'docker push docker.io/shivanipa/my-static-website:latest'
                        sh "docker push docker.io/shivanipa/my-static-website:develop-${buildId}"
                    }
                }
            }
        }
    }
}
