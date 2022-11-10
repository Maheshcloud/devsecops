pipeline {
  agent any
  triggers {
        cron('H H 1,15 1-11 *')
    }
  tools { 
        maven 'maven'  
    }
   stages{
 //   stage('CompileandRunSonarAnalysis') {
 //          steps {	
 //		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=mygit-project -Dsonar.organization=mygit-project -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=$sonar'
 //			}
 //  }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }
    
    
   stage('CompileandRunSonarAnalysis') {
          steps {	
 		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=mygit-project -Dsonar.organization=mygit-project -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=3116af451be6386c60b5af22a992a15400eb9fe3'
 			}
   }
   
   

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
                 script{
                 app =  docker.build("maheshcloud84/devsecops")
                 }
               }
            }
    }
	   
	

	stage('Push') {
            steps {
                script{
                    //docker.withRegistry('https://145988340565.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                    //app.push("latest")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    app.push("${env.BUILD_NUMBER}")
                    }
                }
            }
    	}
	  stage('slack notification'){
       steps{
            slackSend baseUrl: 'https://hooks.slack.com/services/', channel: '#jenkins', color: 'green', message: 'Build info', tokenCredentialId: 'slack_notification', username: 'maheshdte'
          // slackSend "Build Started - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
       }
   
	   
	    post {
       
		    success {
           slackSend "Build deployed successfully - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
       }
       // triggered when red sign
       failure {
           slackSend "Build failed  - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
       }
      
    }
		  }    
  }

/*

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
}*/
