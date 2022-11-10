pipeline {
  agent any
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
    
    stage('slack notification'){
       steps{
           slackSend baseUrl: 'https://hooks.slack.com/services/', channel: '#jenkins', color: 'green', message: 'Build info', tokenCredentialId: 'slack_notification', username: 'maheshdte'
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
	    
  }
}
