pipeline {
  agent any
  stages {
     stage('Tests'){
     steps{
     bat 'gradlew test'
     }
     post {
             success{
             archiveArtifacts 'target/*.json'
             }
     }
     }
     stage('Code Analysis') {
        parallel {
          stage('Code Quality') {
            steps {
              withSonarQubeEnv('sonar') {
                bat(script: 'gradlew sonarqube', returnStatus: true)
              }

              waitForQualityGate true
            }
          }

          stage('Test Reporting') {
            steps {
              cucumber 'reports/*json'
            }
          }
        }
      }
    stage('build') {
      post {
        failure {
          script {
            mail="Build failed"
          }
        }
        success {
          script {
            mail="Build succeeded"
          }
        }
      }
      steps {
        bat 'gradlew build'
        bat 'gradlew javadoc'
        archiveArtifacts 'build/libs/*.jar'
        junit(testResults: 'build/test-results/test/*.xml', allowEmptyResults: true)
      }
    }
    stage("deploy") {
    steps{
    bat 'gradlew publish'
    }
    }
    stage('Notification') {
        parallel{
        stage('Email Notification'){
        steps {
            mail(subject: 'success notification', body: mail, cc: 'kr_djouah@esi.dz', bcc: 'kr_djouah@esi.dz')
        }
        }
        stage('Other Notifications'){
        steps{notifyEvents message: mail, token: '60pmmja-34f4rrv7t3xdkbnhvbdplb-b'
        }
        }
        }
    }
}
}