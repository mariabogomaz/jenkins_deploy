pipeline {
    agent any

    environment {
        JENKINS_IMAGE = 'jenkins/jenkins:lts'
        CONTAINER_NAME = 'jenkins-server'
        VOLUME_NAME = 'jenkins_home'
        CONFIG_DIR = '/var/jenkins_home/custom_config'
    }

    stages {
        stage('Pull Latest Jenkins Image') {
            steps {
                script {
                    sh "docker pull ${JENKINS_IMAGE}"
                }
            }
        }

        stage('Stop and Remove Old Container') {
            steps {
                script {
                    sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    """
                }
            }
        }

        stage('Run New Jenkins Container') {
            steps {
                script {
                    sh """
                    docker run -d --name ${CONTAINER_NAME} -p 8080:8080 -p 50000:50000 \
                    -v ${VOLUME_NAME}:/var/jenkins_home \
                    -v ${CONFIG_DIR}:/var/jenkins_home/casc_configs \
                    -e CASC_JENKINS_CONFIG=/var/jenkins_home/casc_configs \
                    ${JENKINS_IMAGE}
                    """
                }
            }
        }

        stage('Post-Deployment Checks') {
            steps {
                script {
                      // Проверка доступности Jenkins
                    timeout(time: 1, unit: 'MINUTES') {
                        waitUntil {
                            def response = sh(script: "curl -s -o /dev/null -w '%{http_code}' ${JENKINS_URL}", returnStatus: true)
                            return response == 200
                        }
                    }
                    echo 'Jenkins deployed successfully'

                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
