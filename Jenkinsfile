pipeline {
    agent any
    environment {
        DOCKER_HUB_LOGIN = credentials('pin1')
        REGISTRY = 'enardelg'
    }
        stage('docker build') {
            steps {
                sh 'docker build -t node-devops-git:v1 .'
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW
                    docker tag node-devops-git:v1 $REGISTRY/node-devops-git:v1
                    docker push $REGISTRY/node-devops-git:v1
                '''
            }
        }
    }
}
