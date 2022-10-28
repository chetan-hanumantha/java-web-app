pipeline {
  agent any
  tools {
    jdk "java-8"
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    CI = true
    ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')
  }
  stages {
    stage('Build') {
      steps {
        sh './mvnw clean install'
      }
    }
    stage('Upload to Artifactory') {
      steps {
        sh 'jfrog rt upload --url http://192.168.1.230:8082/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/demo-0.0.1-SNAPSHOT.jar java-web-app/'
      }
    }
  }
}