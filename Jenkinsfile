pipeline {
    agent any

    environment {
        DOCKERHUB_creds = credentials('docker')
        DOCKER_IMAGE = "athakur0610/cw2-server:1.0"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker Image....'
                sh 'docker build --tag $DOCKER_IMAGE .'
                echo 'Docker Image built successfully!'
            }
        }

        stage('Test Container') {
            steps {
                echo 'Testing Docker Image...'
                sh '''
                docker image inspect $DOCKER_IMAGE
                docker run -d --name test-container -p 8081:8080 $DOCKER_IMAGE
                docker ps
                docker stop test-container
                docker rm test-container
                '''
            }
        }

        stage('DockerHub Login') {
            steps {
                sh 'echo $DOCKERHUB_creds_PSW | docker login -u $DOCKERHUB_creds_USR --password-stdin'
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['jenkins-ssh-key']) {
                    sh """
                        ssh ec2-user@ec2-54-205-227-151.compute-1.amazonaws.com '
                            kubectl set image deployment/devopscw2 cw2-server=$DOCKER_IMAGE &&
                            kubectl rollout status deployment/devopscw2
                        '
                    """
                }
            }
        }
    }
}
