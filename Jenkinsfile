pipeline {
    agent any

    environment {
        DOCKERHUB_creds = credentials('docker')
        DOCKER_IMAGE = "houssameddineb/my-app:0.1"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git 'https://github.com/Avi-techAI/cw2-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker Image....'
                sh 'docker build --tsg $DOCKER_IMAGE'
                echo 'Docker Image built succesfully!'
            }
        }

        stage('Test Container') {
            steps {
                echo 'Testing Docker Image...'
                sh '''
                docker image inspect $DOCKER_IMAGE
                docker run -d --name test-container -p 8081:8080 -d $DOCKER_IMAGE
                docker ps
                docker stop test-container
                docker rm test-container
                '''
            }
        }
         stage('DockerHub Login') {
            steps {
                sh 'echo $DOCKERHUB_creds_PSW | docker login -u $DOCKERHUB_creds_USR --password-stdin
            }
        }

        

        stage('Push to DockerHub') {
            steps {
                sh 'docker push houssameddineb/my=app
                }
            }
        } 
}
