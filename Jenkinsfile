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
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push pixcs13/lbg:${BUILD_NUMBER}
                docker push pixcs13/lbg:latest
                docker rmi pixcs13/lbg:${BUILD_NUMBER}
                docker rmi pixcs13/lbg:latest
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                ssh jenkins@maria-deploy <<EOF
                export PORT=${PORT}
                export VERSION=${BUILD}

                docker stop lbg-api && echo "Stopped lbg-api" || echo "lbg-api not running"
                docker rm lbg-api && echo "removed lbg-api" || echo "lbg-api does not exist"
    
                docker run -d -p 80:${PORT} -e PORT=${PORT} --name lbg-api pixcs13/lbg:2.0
                '''
            }

        }

    }