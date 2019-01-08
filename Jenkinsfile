node {
		   def tag = ""
		   def imageTag = "us.gcr.io/${project}/happistarfrontstage:${tag}"
  try {


		 
		   stage('Clone Front End Project') {
			 checkout([$class: 'GitSCM', branches: [[name: '*/thai-web']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: './']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'toffcso-github-com',url: 'https://github.com/ToubroInfo/AngularUI.git']]])
			 tag = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
			 imageTag = imageTag + tag 
		   }
		   
		   stage('Build Angular Project') {
			 sh("ls")

			  dir ('client') {
				
				sh("npm install")
				sh("npm install @angular/cli@1.7.4 --no-optional")
				
				sh("npm install @angular-devkit/core")
				sh("npm install @ngtools/json-schema")
				sh("npm install typescript@'>=2.4.2 <2.7.0'")
				sh("npm install @ngrx/store")
				sh("npm install @ngx-translate/core")
				  
				sh("node_modules/.bin/ng -v")
				sh("npm run build-alphastaging")
			  }
			 
			 sh("cp -R ./client/dist/ ./server")
		   }
		   
		   stage('Bake Docker Image') {
			 sh("echo ${imageTag}")
		
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
 
}
