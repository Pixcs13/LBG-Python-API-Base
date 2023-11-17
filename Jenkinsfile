pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t pixcs13/wk2-project .
                docker build -t pixcs13/wk2-nginx nginx
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push pixcs13/wk2-project
                docker push pixcs13/wk2-nginx
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                ssh jenkins@maria-deploy2 <<EOF
                docker pull pixcs13/wk2-project
                docker pull pixcs13/wk2-nginx
                docker stop week2-app && echo "Stopped week2-app" || echo "week2-app not running"
                docker rm week2-app && echo "removed week2-app" || echo "week2-app does not exist"
                docker stop nginx && echo "Stopped nginx" || echo "nginx not running"
                docker rm nginx && echo "removed nginx" || echo "nginx does not exist"
                docker network rm week2-net && echo "removed network" || echo "network already removed"
                docker network create week2-net
                docker run -d --name week2-app-1 --network week2-net pixcs13/wk2-project
                docker run -d --name week2-app -p 80:80 pixcs13/wk2-project
                '''
            }

        }

    }