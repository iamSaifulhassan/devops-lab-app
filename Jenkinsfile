pipeline {
    agent any

    environment {
        IMAGE_NAME = 'devops-lab-app'
        CONTAINER_NAME = 'devops-lab-app-ci'
        HOST_PORT = '5001'
        APP_PORT = '5000'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker build -t ${IMAGE_NAME}:latest .'
                    } else {
                        bat 'docker build -t %IMAGE_NAME%:latest .'
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    if (isUnix()) {
                        sh '''
                            if [ $(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                              docker rm -f ${CONTAINER_NAME}
                            fi
                            docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${APP_PORT} ${IMAGE_NAME}:latest
                        '''
                    } else {
                        bat '''
                            for /f "tokens=*" %%i in ('docker ps -aq -f name=%CONTAINER_NAME%') do set FOUND=%%i
                            if defined FOUND docker rm -f %CONTAINER_NAME%
                            docker run -d --name %CONTAINER_NAME% -p %HOST_PORT%:%APP_PORT% %IMAGE_NAME%:latest
                        '''
                    }
                }
            }
        }

        stage('Verify Container') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker ps --filter name=${CONTAINER_NAME}'
                    } else {
                        bat 'docker ps --filter name=%CONTAINER_NAME%'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed. App is running on Jenkins host port ${HOST_PORT}.'
        }
        failure {
            echo 'Pipeline failed. Check build logs for details.'
        }
    }
}
