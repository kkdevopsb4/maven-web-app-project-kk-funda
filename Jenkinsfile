pipeline {
    agent any

    environment {
        DEPLOY_SERVER = '34.232.91.67'
        DEPLOY_USER = 'ec2-user'
        DEPLOY_DIR = '/opt/tomcat/webapps/'
    }

    tools {
        maven 'maven3.9.9'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'Devlop', credentialsId: '84b9e77b-9119-44b3-8f82-9239c7f2fc0f', url: 'https://github.com/usharaniwin/maven-web-app-project.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        // Optional SonarQube Stage (commented out)
        /*
        stage('Sonar') {
            steps {
                sh 'mvn clean sonar:sonar'
            }
        }
        */

        stage('Nexus') {
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage('SSH_Deploy') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_SERVER} "sudo chmod -R 777 ${DEPLOY_DIR}"
                        scp -o StrictHostKeyChecking=no target/maven-web-application.war ${DEPLOY_USER}@${DEPLOY_SERVER}:/tmp/
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_SERVER} "sudo mv /tmp/maven-web-application.war ${DEPLOY_DIR}"
                    '''
                }
            }
        }
    }
}
