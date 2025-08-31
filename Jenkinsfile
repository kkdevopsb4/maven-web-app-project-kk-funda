pipeline {
  agent any
  environment {
    MAVEN_HOME = tool name: 'maven-3.9.9', type: 'maven'
    WAR = 'target/maven-web-application.war'
    TOMCAT_URL = "http://54.82.124.135:8080/manager/text/deploy?path=/appName&update=true"
    TOMCAT_USER = 'admin'
    TOMCAT_PASSWORD = 'admin'
    TOMCAT_PATH = '/opt/apache-tomcat-9.0.100/webapps'
    TMP_PATH = '/tmp/maven-web-application.war'
    MYCHANNEL = "durga-prac"
  }
  stages {
    stage('checkout') {
      steps {
        git branch: 'UAT', credentialsId: 'git_cred', url: 'https://github.com/Ducheruk/maven-web-app-project-kk-funda.git'
      }
    }
    stage('build') {
      steps {
        sh "${env.MAVEN_HOME}/bin/mvn clean install"
      }
    }
    stage('sonar') {
      steps {
        sh "${env.MAVEN_HOME}/bin/mvn sonar:sonar"
      }
    }
    stage('nexus') {
      steps {
        sh "${env.MAVEN_HOME}/bin/mvn clean deploy"
      }
    }
    stage('copy WAR to tomcat') {
      steps {
        echo "(copy war file: ${env.WAR} to tomcat server)"
        sshagent(['ssh_pem_keys']) {
          sh "scp -o StrictHostKeyChecking=no ${env.WAR} ubuntu@54.82.124.135:${env.TMP_PATH}"
          sh "ssh -o StrictHostKeyChecking=no ubuntu@54.82.124.135 'sudo cp ${env.TMP_PATH} ${env.TOMCAT_PATH}'"
        }
      }
    }
    stage('slack notification') {
      steps {
        slackSend(channel: env.MYCHANNEL, color: 'good', message: "super success for ${env.JOB_NAME} #${env.BUILD_NUMBER}")
      }
    }
  }
  post {
    failure {
      slackSend(channel: env.MYCHANNEL, color: 'danger', message: "bad failure for ${env.JOB_NAME} #${env.BUILD_NUMBER}")
    }
  }
}
