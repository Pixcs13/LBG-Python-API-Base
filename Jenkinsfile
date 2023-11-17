pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t pixcs13/wk2-project .
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push pixcs13/wk2-project
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                docker pull pixcs13/wk2-project
                docker stop week2-app && echo "Stopped week2-app" || echo "week2-app not running"
                docker rm week2-app && echo "removed week2-app" || echo "week2-app does not exist"
                docker run -d --name week2-app -p 80:80 pixcs13/wk2-project
                '''
            }

        }

    }