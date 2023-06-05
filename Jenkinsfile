pipeline {
    agent {
        docker {
            image 'maven:3.9.0-eclipse-temurin-11'
            args '-v /root/.m2:/root/.m2'
        }
    }

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
                sh "ssh -i ${SERVER_PRIVATE_KEY} ${SERVER_USERNAME}@${SERVER_HOST} "
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
