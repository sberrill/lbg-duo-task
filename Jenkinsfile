pipeline {
    agent any
    stages {       
        stage('cleanup') {
            steps {
                sh '''
                if docker logs nginx; then
                    if docker exec nginx ls; then
                        docker stop nginx
                    else
                        sleep 1
                    fi
                    docker rm nginx
                else
                    sleep 1
                fi
                if docker logs flask-app; then
                    if docker exec flask-app ls; then
                        docker stop flask-app
                else
                    sleep 1
                    fi
                    docker rm flask-app
                 else
                    sleep 1
                fi
                '''
            }
        }            
        stage('Build') {
            steps {
                sh 'docker build -t duo-task:v1 .'
            }
        }
        stage('run') {
            steps {
                sh '''
                docker network inspect duo-net && sleep 1 || docker network create duo-net
                docker run -d --name flask-app --network duo-net duo-task:v1
                docker run -d -p 80:80 --network duo-net --mount type=bind,source=$(pwd)/nginx.conf,dst=/etc/nginx/nginx.conf --name nginx nginx:alpine
                '''
            }
        }
    }
}
