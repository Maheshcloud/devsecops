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
                 app =  docker.build("test")
                 }
               }
            }
    }
	   
	  stage('slack notification'){
       steps{
           slackSend baseUrl: 'https://hooks.slack.com/services/', channel: '#jenkins', color: 'green', message: 'Build info', tokenCredentialId: 'slack_notification', username: 'maheshdte'
           //slackSend "Build Started - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
       }
   }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://798616169137.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                    app.push("latest")
                   // docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                   // app.push("${env.BUILD_NUMBER}")
                    }
                }
            }
    	}
	   
	   stage('Kubernetes Deployment of ASG Bugg Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		  sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	      }
   	}
	    
  }
}
