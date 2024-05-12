pipeline {
    agent any
    environment {
        DOCKER_HUB_LOGIN = credentials('pin1')
        REGISTRY = 'enardelg'
        IMAGE = 'node-devops-auto'
        SERVER = 'ec2-user@54.163.44.87'
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
                    echo "Version: $VERSION"
                    echo "Image: $IMAGE"
                    echo $VERSION > version.txt
                    docker build -t $IMAGE:$(cat version.txt) .
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

       stage('Update docker-compose') {
            steps {
                sh '''
                   sed -i -- "s/REGISTRY/$REGISTRY/g" docker-compose.yml
                   sed -i -- "s/APP_NAME/$IMAGE/g" docker-compose.yml
                   sed -i -- "s/TAG/$(cat version.txt)/g" docker-compose.yml
                   cat docker-compose.yml
                '''
            }
        }

      stage('Deploy to AWS') {
            steps {
               sshagent(['aws-ssh']){
                   sh 'scp -o StrictHostKeyChecking=no docker-compose.yml $SERVER:/home/ec2-user'
                   sh 'ssh $SERVER ls -lrt'
                   sh 'ssh $SERVER docker-compose up -d'
               }
            }
        }
    }
}
