    pipeline {
    agent any
    environment {
        PORT = "8000"
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t pixcs13/lbg:${BUILD_NUMBER} --build-arg PORT=${PORT} .
                docker tag pixcs13/lbg:${BUILD_NUMBER} pixcs13/lbg:latest
                docker push pixcs13/lbg:${BUILD_NUMBER}
                docker push pixcs13/lbg:latest
                docker rmi pixcs13/lbg:${BUILD_NUMBER}
                docker rmi pixcs13/lbg:latest
                '''
           }
        }
        stage("generate nginx.conf") {
            steps {
                sh '''
                cat - > nginx.conf <<EOF
                events {}
                http {
                    server {
                        listen 80;
                        location / {
                            proxy_pass http://lbg-api:${PORT};
                        }
                    }
                }
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                scp nginx.conf jenkins@maria-deploy:/home/jenkins/nginx.conf
                ssh jenkins@maria-deploy <<EOF
                export PORT=${PORT}
                export VERSION=${BUILD_NUMBER}
                docker pull pixcs13/lbg:${BUILD_NUMBER}
                docker pull pixcs13/lbg:latest
                docker stop lbg-api && echo "stopped" || echo "not running"
                docker rm lbg-api && echo "removed" || echo "already removed"
                docker stop nginx && echo "stopped" || echo "not running"
                docker rm nginx && echo "removed" || echo "already removed"
                docker network inspect proj && echo "network exists" || docker network create proj
                docker run -d -e PORT=${PORT} --name lbg-api --network proj pixcs13/lbg
                docker run -d -p 80:80 --name nginx --network proj --mount type=bind,source=/home/jenkins/nginx.conf,target=/etc/nginx/nginx.conf nginx:alpine
                '''
            }
        }
    }
}