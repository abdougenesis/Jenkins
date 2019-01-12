pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'gradle build'
        bat 'gradle myJavaDocs'
        archiveArtifacts(artifacts: 'build/libs/*.jar , build/docs/javadoc/*', onlyIfSuccessful: true)
      }
    }
    stage('Mail Notification') {
      steps {
        mail(subject: 'build finished', body: 'build finished', to: 'fa_djellal@esi.dz')
      }
    }
    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            script {
              def sonarScanner = tool name: 'sonarscanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
              bat "${sonarScanner}/bin/sonar-scanner -e -Dsonar.host.url=xxx"
              }
            }

            waitForQualityGate true
          }
        }
        stage('Test Reporting') {
          steps {
            jacoco(buildOverBuild: true)
          }
        }
      }
    }
    stage('Deployment') {
      steps {
        bat 'gradle uploadArchives'
      }
    }
    stage('Slack Notification') {
      steps {
        slackSend(channel: 'buildsjenkins', color: '#ffffff', message: 'jenkins reached slack build ')
      }
    }
  }
}
