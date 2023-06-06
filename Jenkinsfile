pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jenkins-docker"
    }

    stages {
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
//                withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
//                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
//                }
//                sh 'docker build -t ${DOCKER_IMAGE}:latest .'
//                sh 'docker push ${DOCKER_IMAGE}:latest'
//
//                sh 'docker image rm ${DOCKER_IMAGE}:latest'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'SSH_SERVER', keyFileVariable: 'SERVER_PASS', usernameVariable: 'SERVER_USERNAME')]) {
                    sh "sshpass -p ${SERVER_PASS} ssh ${SERVER_USERNAME}@${env.SERVER_HOST} 'cd /path/to/app && ./deploy.sh'"
                }
            }
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
