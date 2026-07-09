pipeline {
    agent any

    tools {
        jdk 'JDK17'          // Change to your JDK tool name in Jenkins
        maven 'Maven3'       // Change to your Maven tool name in Jenkins
    }

    environment {
        GIT_URL = 'https://github.com/Erniesmash/hello-world-webapp.git'

        TOMCAT_URL = 'http://localhost:9090'
        TOMCAT_CREDENTIALS = 'tomcat-manager'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: "${GIT_URL}"
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }

            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                bat 'mvn package'
            }
        }

        stage('Deploy') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: "${TOMCAT_CREDENTIALS}",
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {

                    bat '''
                    curl -v ^
                    -u %USER%:%PASS% ^
                    -T target\\*.war ^
                    "%TOMCAT_URL%/manager/text/deploy?path=/myapp&update=true"
                    '''
                }
            }
        }
    }

    post {

        success {
            echo 'Deployment Successful!'
        }

        failure {
            echo 'Deployment Failed!'
        }

        always {
            deleteDir()
        }
    }
}