pipeline {
	agent { node 'slave'}
	tools {
		jdk 'java17'
		maven 'maven3'
	}
	environment {
	    APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "rizgh"
            DOCKER_PASS = 'dockerhub'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
		stage("Build & Push Docker Image") {
            		steps {
                	script {
                    	docker.withRegistry('',DOCKER_PASS) {
                        	docker_image = docker.build "${IMAGE_NAME}"
                    		}
	                docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
	                   		}
        	        	}
            		}
       		}
		stage("Trivy Scan") {
    			steps {
        		script {
            			sh '''
                		docker run --rm \
                    		-v /var/run/docker.sock:/var/run/docker.sock \
                    		-v /path/to/local/cache:/root/.cache/trivy \
                    		aquasec/trivy image rizgh/register-app-pipeline:latest \
                    		--no-progress --scanners vuln \
                    		--exit-code 0 --severity HIGH,CRITICAL --format table
            			'''
        			}
    			}
		}
		stage("Trigger CD Pipeline") {
            		steps {
                	script {
                    def cdBuild = build job: 'cd-pipeline', parameters: [
                        string(name: 'IMAGE_TAG', value: "${IMAGE_TAG}")
                    ]
                }
            }
        }
         }
}

