pipeline {
	agent { node 'slave'}
	tools {
		jdk 'java17'
		maven 'maven3'
	}
	stages {
		stage("cleanup workspace") {
			steps {
				cleanWs()
			}
		}
		stage("checkout from scm") {
			steps {
				git branch: 'main', credentialsId: 'github', url: 'https://github.com/rizgh/register-app/'
			}
		}
		stage("Build Apps") {
			steps {
				sh "mvn clean package"
			}
		}
		stage("Test Apps") {
			steps{
				sh "mvn test"
			}
		}
		stage("SonarQube Analysis"){
           		steps {
	           		script {
		        	withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
                        	sh "mvn sonar:sonar"
			        	}
	        	   	}	
           		}
       		}
	       stage("Quality Gate"){
        		steps {
               			script {
                    		waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                		}	
            		}
	        }
	}
}
