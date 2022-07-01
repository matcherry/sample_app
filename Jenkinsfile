node {
    try {
    def gitRepoUrl = 'https://github.com/matcherry/sample-app.git'
    notifyBuild('STARTED')
    stage('Checkout') {
        git(url: gitRepoUrl, branch: 'master')   
        }
    stage('Preparation') {
        catchError(buildResult: 'SUCCESS') {
        sh 'docker stop samplerunning'
        sh 'docker rm samplerunning'
    }
    }
    stage('Build') {
        build 'sample-app'
    }
    stage('Results') {
        build 'test-sample'
    }
    stage('Testing') {
        sh 'bzt taurus-test.yaml -report'
            
    }
    
  } catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}