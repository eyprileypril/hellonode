node {
		   def tag = ""
		   def imageTag = "us.gcr.io/${project}/happistarfrontstage:${tag}"
  try {
    notifyBuild('STARTED', "${tag}")  

		 
		   stage('Clone Front End Project') {
			 checkout([$class: 'GitSCM', branches: [[name: '*/thai-web']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: './']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-april',url: 'https://github.com/ToubroInfo/AngularUI.git']]])
			 tag = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
			 imageTag = imageTag + tag 
		   }
		   
		   stage('Build Angular Project') {
			 sh("ls")

			  dir ('client') {
				
				sh("npm install")
			  }

		
		   }
		   


		   stage('Print image tag ID') {
			  sh("echo success staging build and published ${imageTag}")
			  sh("echo staging version tag: ${tag}")
		   }
		   
	} catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result, "${tag}")
  }
}
 
def notifyBuild(String buildStatus = 'STARTED', tag) {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'
 
  // Default values
  def baseSlackUrl = 'https://iaoc.slack.com/services/hooks/jenkins-ci/'
  def slackChannel = 'builds_notification'
  def teamSlackDomain = 'iaoc' 
  def tokenCredentials = 'jenkins-slack-integration'
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subj = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def details = '''${SCRIPT, template="groovy-html.template"}'''
  def summary = """
  version tag: ${tag}  
  ${details}
  """
  
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
  //slackSend (baseUrl: baseSlackUrl, channel: slackChannel, teamDomain: teamSlackDomain, tokenCredentialId: tokenCredentials, //color: colorCode, message: subj)

  // Send Emails only postbuild
  if (buildStatus == 'TARTED') {
  emailext (
	  to: "devops-mnl@emeritussoftware.ltd;qa-distribution-list@emeritussoftware.ltd;dev-ph@emeritussoftware.ltd;ba@emeritussoftware.ltd",
      from: "jenkins@aos.services",
      subject: subj,
      body: summary,
      recipientProviders: [[$class: 'DevelopersRecipientProvider']]
    )
	}
}

	
	
