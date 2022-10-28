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
    UPLOAD_LOCATION = "java-web-app/"
  }
  stages {
    stage('Build') {
      steps {
        echo "Branch Name: ${env.GIT_BRANCH}"
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
        echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
        echo "Job Name is ${env.JOB_NAME}"
        echo "Build Number is ${env.BUILD_NUMBER}"
        sh '/opt/homebrew/bin/jfrog rt ping --url http://localhost:8081/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN}'
        sh '/opt/homebrew/bin/jfrog rt upload --url http://localhost:8081/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/demo-0.0.1-SNAPSHOT.jar java-web-app/'
        sh '/opt/homebrew/bin/jfrog rt bp --url http://localhost:8081/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN}' // publish build info
      }
    }
//     stage('Upload') {
//       steps {
//         script {
//           def server = Artifactory.server 'artifactory'
//           def uploadSpec = """{
//             "files": [{
//               "pattern": "(*.zip | *.tar.gz | *.jar)",
//               "target": "${env.UPLOAD_LOCATION}",
//               "recursive": "true",
//               "flat": "false",
//               "props": "Version=${env.BUILD_NUMBER};JobName=${env.JOB_NAME}"
//             }]
//           }"""
//
//           server.upload(uploadSpec)
//         }
//       }
//     }
  }
}