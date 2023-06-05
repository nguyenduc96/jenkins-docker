pipeline {
    agent none

    environment {
        DOCKER_IMAGE = "jenkins-docker"
    }

    stages {
       agent {
               docker {
                   image 'maven:3.8.3-openjdk-11'
                   args '-v /var/run/docker.sock:/var/run/docker.sock'
               }
           }

        stage('Test') {
            steps {
                // Run tests using Maven
                sh 'mvn test'
            }
        }


        stage('Build') {
            steps {
                // Build the project using Maven
                sh 'mvn clean install'
                withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                }
                sh 'docker build -t ${DOCKER_IMAGE}:latest .'
                sh 'docker push ${DOCKER_IMAGE}:latest'

                sh 'docker image rm ${DOCKER_IMAGE}:latest'
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the application to your server or container using SSH
                sh "ssh -i ${SERVER_PRIVATE_KEY} ${SERVER_USERNAME}@${SERVER_HOST} 'your-deployment-command'"
            }
        }

        post {
            success {
                echo "SUCCESS"
            }
            failure {
                echo "FAILED"
            }
        }
    }
}
