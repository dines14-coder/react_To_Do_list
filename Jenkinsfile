pipeline {
    agent any

    environment {
                REACT_IMAGE = "dinesh14coder/hepl:react-to-do${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials("dock-cred")
                REPLACE="hepl:react-to-do${BUILD_NUMBER}"
    }

    
    stages {

        stage('Checkout') {
            steps {
                sh 'ls -ltr'
            }
        }

        stage('Static Code Analysis') {
            environment {
                SONAR_URL = "http://localhost:9000"
            }
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=react-todo \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=$SONAR_URL \
                          -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }
        stage('Build Image and Push') {
            steps {
                script {
                        sh 'docker --version'
                        sh 'whoami'
                        sh 'cd daily_app && docker build -t ${REACT_IMAGE} .' 
                        def dockerImage = docker.image("${REACT_IMAGE}")
                        withDockerRegistry([credentialsId: 'dock-cred', url: 'https://index.docker.io/v1/']) { 
                            dockerImage.push()
                        }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'sed -i -E "s/hepl:.*/${REPLACE}/g" docker-compose.yaml'
                sh "ls -ltr"
				sh 'docker-compose down || true'
				sh 'docker-compose -f docker-compose.yaml up -d'
            }
        }

        stage('Update Deployment File') {
            environment {
                GIT_REPO_NAME = "react_To_Do_list"
                GIT_USER_NAME = "dines14-coder"
		        
            }
            steps {
                withCredentials([string(credentialsId: 'hepl', variable: 'GITHUB_TOKEN')]) {
                    sh '''
                        git config user.email "dvrdineshdvrdinesh728@gmail.com"
                        git config user.name "dines14-coder"
                        git add docker-compose.yaml
                        git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                        git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    '''
                }
            }
        }


    }
}
