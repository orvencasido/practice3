pipeline {
    agent any 

    environment {
        DOCKER_IMAGE = "orvencasido/practice3"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh """
                        docker rm -f test_container || true
                        docker run -d -p 80:80 --name test_container ${DOCKER_IMAGE}
                        curl -s http://13.212.188.101:80 | grep <title>
                        docker rm -f test_container
                    """
                }
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub_creds', usernameVariable: 'DOCKERHUB_USER', passwordVaraible: 'DOCKERHUB_PASS')]) {
                    sh """
                        echo ${DOCKERHUB_PASS} | docker login -u ${DOCKERHUB_USER} --pasword-stdin
                        docker push ${DOCKER_IMAGE}
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh """
                        docker rm -f practice3 || true
                        docker run -d -p 80:80 --name practice3 ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        } 

        failure {
            echo "Pipeline Failed!"
        }

        success {
            echo "Pipeline Sucessful!"
        }
    }
}