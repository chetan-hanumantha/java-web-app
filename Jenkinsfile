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
    JFROG_CLI_BUILD_NAME = "${env.JOB_NAME}"
    JFROG_CLI_BUILD_NUMBER = "${env.BUILD_NUMBER}"
  }
  stages {
    stage('Build') {
      steps {
        sh './mvnw clean install'
      }
      post {
        success {
            archiveArtifacts 'target/*.jar'
        }
      }
    }
    stage('Upload to Artifactory') {
      steps {
        sh 'echo "Job Name is ${env.JOB_NAME}"'
        sh 'echo "Build Number is ${env.BUILD_NUMBER}"'
        sh 'jfrog rt ping'
        sh 'jfrog rt upload --url http://http://localhost:8082/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/demo-0.0.1-SNAPSHOT.jar java-web-app/'
        sh 'jfrog rt bp' // publish build info
      }
    }
  }
}