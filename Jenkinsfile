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
                docker stop wk2-app && echo "Stopped wk2-app" || echo "wk2-app not running"
                docker rm wk2-app && echo "removed wk2-app" || echo "wk2-app does not exist"
                docker stop wk2-nginx && echo "Stopped nginx" || echo "nginx not running"
                docker rm wk2-nginx && echo "removed nginx" || echo "nginx does not exist"
                docker network rm wk2-net && echo "removed network" || echo "network already removed"
                docker network create wk2-net
                docker run -d --name wk2-app --network wk2-net pixcs13/wk2-project
                docker run -d --name wk2-nginx --network wk2-net -p 80:80 pixcs13/wk2-nginx
                '''
            }

        }

    }