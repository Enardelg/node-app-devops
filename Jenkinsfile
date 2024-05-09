pipeline {
    agent any
    environment{
        APPNAME = 'node-app-demo'
        REGISTRY = 'enardelg'
        DOCKER_HUB_LOGIN = credentials('docker-hub')
    }
    stages { // el principal donde se arman la tuberia 
        //CI
        stage('Init') {
            agent{
                docker {
                    image 'node:erbium-alpine'
                    args '-u root:root'
                }
            }
            steps {
                echo "Init"
                sh 'npm install'
            }
        }
        stage('Test') {
            agent{
                docker {
                    image 'node:erbium-alpine'
                    args '-u root:root'
                }
            }
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh 'npm run test'
                }
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t $APPNAME:latest .'
                sh 'docker tag $APPNAME:latest $REGISTRY/$APPNAME:latest'

            }
        }
        // CD
        stage('Deploy') {
            steps {
                echo 'Docker Login'
                sh 'docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW'
                sh 'docker push $REGISTRY/$APPNAME:latest'
            }
        }
        stage('Notificaction') {
            steps {
                echo 'Telegram/slack/discord/team-...'
            }
        } 
    }
}