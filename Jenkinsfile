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
    // Assuming the SSH key credentials are stored securely in Jenkins using the Credentials Plugin
           
              withCredentials([ssh(fileId: 'aws-ssh', username: 'ec2-user')]) {
      // Enable strict host key checking for secure connection
                  sh 'scp -o StrictHostKeyChecking=yes docker-compose.yml ec2-user@54.163.44.87:/home/ec2-user'

      // Verify successful transfer before proceeding
                  sh 'ssh ec2-user@54.163.44.87 ls -lrt docker-compose.yml' // Check if the file exists

      // Execute docker-compose up -d on the remote server
                  sh 'ssh ec2-user@54.163.44.87 docker-compose up -d'
                }
              }
            }
    }
}
