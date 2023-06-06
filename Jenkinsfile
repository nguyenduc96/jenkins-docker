pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jenkins-docker"
    }

    stages {
        stage('TEST') {
            agent {
                docker {
                    image 'maven:3.8.6-openjdk-11'
                    args '-u 0:0 /tmp:/root/.cache'
                }
            }
            steps {
                // Run tests using Maven
                sh 'mvn test'
            }
        }


        stage('BUILD') {
            environment {
                TAG_IMAGE = "${GIT_COMMIT.substring(0,7)}"
            }
            steps {
                // Build the project using Maven
                sh 'mvn clean install'
                withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                }
                sh 'docker build -t ${DOCKER_IMAGE}:${TAG_IMAGE} .'
                sh 'docker push ${DOCKER_IMAGE}:${TAG_IMAGE}'

                sh 'docker image rm ${DOCKER_IMAGE}:${TAG_IMAGE}'
            }
        }

        stage('DEPLOY') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'KeyServer', keyFileVariable: 'SERVER_PRIVATE_KEY', usernameVariable: 'SERVER_USERNAME')]) {
                    sh "ssh -i ${SERVER_PRIVATE_KEY} ${SERVER_USERNAME}@${env.SERVER_HOST} 'cd /path/to/app && ./deploy.sh'"
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
