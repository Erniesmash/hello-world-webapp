pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }

    environment {
        GIT_URL = 'https://github.com/Erniesmash/hello-world-webapp'
        GIT_CREDENTIALS = 'git-ssh'

        TOMCAT_URL = 'http://localhost:9090'
        TOMCAT_CREDENTIALS = 'tomcat-manager'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: "${GIT_CREDENTIALS}",
                    url: "${GIT_URL}"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }

            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
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

                    sh '''
                    curl -v \
                    -u $USER:$PASS \
                    -T target/*.war \
                    "http://localhost:8080/manager/text/deploy?path=/myapp&update=true"
                    '''
                }
            }
        }
    }

    post {

        success {
            echo "Deployment Successful!"
        }

        failure {
            echo "Deployment Failed!"
        }

        always {
            cleanWs()
        }
    }
}