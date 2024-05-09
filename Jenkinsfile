pipeline {
    agent any
    environment {
        DOCKER_HUB_LOGIN = credentials('pin1')
        REGISTRY = 'enardelg'
        IMAGE = 'node-devops-auto'
    }
     stages {
         
        stage('Init'){
            agent {
                docker {
                    image 'node:alpine'
                    args '-u root:root'
                }
            }
            steps {
                sh 'npm install'
            }
        }

        stage('Test'){
            agent {
                docker {
                    image 'node:alpine'
                    args '-u root:root'
                }
            }
            steps {
                sh 'npm run test'
            }
        }
         
        stage('docker build') {
            steps {
                sh '''
                    VERSION=$(jq --raw-output .version package.json)
                    echo $VERSION >version.txt
                    docker build -t $IMAGE:$(cat version.txt)
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW
                    docker tag $IMAGE:$(cat version.txt) $REGISTRY/$IMAGE:$(cat version.txt)
                    docker push $REGISTRY/$IMAGE:$(cat version.txt)
                '''
            }
        }
    }
}
