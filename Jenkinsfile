pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                sh 'ls -ltr'
                // or use git checkout
                // git branch: 'main', url: 'https://github.com/dines14-coder/react_To_Do_list.git'
            }
        }

        stage('Static Code Analysis') {
            environment {
                SONAR_URL = "http://localhost:9000"
            }
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh '''
                        cd daily_app
                        mvn sonar:sonar \
                          -Dsonar.login=$SONAR_AUTH_TOKEN \
                          -Dsonar.host.url=$SONAR_URL
                    '''
                }
            }
        }
    }
}
