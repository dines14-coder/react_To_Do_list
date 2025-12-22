pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // git branch: 'main', url: 'https://github.com/dines14-coder/react_To_Do_list.git'
                sh "ls -ltr"
            }
        }
        stage('Build Image and Push') {
            environment {
                DOCKER_IMAGE = "dvrdinesh/rmkvdemo:angular${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials("hub-cred")
            }
            steps {
                script {
                    sh 'docker --version'
                    sh 'whoami'
                    sh "ls -ltr"
                    sh "cd daily_app && docker build -t ${DOCKER_IMAGE} ."
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    sh "docker tag dvrdinesh/rmkvdemo:angular${BUILD_NUMBER} dvrdinesh/rmkvdemo:angular${BUILD_NUMBER}"
                    docker.withRegistry('https://index.docker.io/v1', 'hub-cred') {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
