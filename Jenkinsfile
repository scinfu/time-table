#!groovy

#!groovy

pipeline {
  agent any

  stages {
    stage('Checkout') { 
      steps {
        checkout scm
      }
    }

    stage('Dependecies') { 
      steps { 
        sh '/usr/local/bin/pod update'
      }
    }

    stage('Running Tests') { 
      steps { 
        echo 'Running Tests'
        sh 'fastlane scan'
      }
    }

    stage('Generating Docs') {
      steps {
        parallel (
          "Jazzy": {
            sh 'jazzy'
            sh 'rm -rf /srv/deploy/bikemap-docs/ios/source'
            sh 'cp -a docs/source/. /srv/deploy/bikemap-docs/ios/reference'
          },
          "Mkdocs": {
            sh 'cd docs/concepts && mkdocs build'
            sh 'rm -rf /srv/deploy/bikemap-docs/ios/concepts'
            sh 'cp -a docs/concepts/site/. /srv/deploy/bikemap-docs/ios/concept'
          }
        )
      }
    }

    stage('Distribution') {
       steps {
         // Building and packaging the app
         sh 'fastlane gym --silent'
         
         // Uploading to TestFlight/Hockey/etc.
         sh 'fastlane deliver'
       }
    }
  }

  post {
    always {
      junit 'test_output/report.junit'
      sh 'rm -rf build'
    }
    success {
      // Slack notification about the success
      notifyBuild()
    }
    failure {
      // Slack notification about the failure
      notifyBuild('ERROR')
    }
  }
}

// Slack notification with status and code changes from git
def notifyBuild(String buildStatus = 'SUCCESSFUL') {
  buildStatus = buildStatus

  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def changeSet = getChangeSet()
  def message = "${subject} \n ${changeSet}"

  if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  slackSend (color: colorCode, message: message)
}

@NonCPS

// Fetching change set from Git
def getChangeSet() {
  return currentBuild.changeSets.collect { cs ->
    cs.collect { entry ->
        "* ${entry.author.fullName}: ${entry.msg}"
    }.join("\n")
  }.join("\n")
}
