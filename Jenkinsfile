/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent { label 'slave' }
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
        stage('cleanup workspace') {
            steps {
                cleanWs()
            }
        }
        stage('checkout') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/rizgh/register-app/'
            }
        }
        stage('build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Testapp') {
            steps {
                sh 'mvn test'
            }
        }
        stage('sonar-analysis') {
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
            		sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image rizgh/register-app-pipeline:latest --no-progress --scanners vuln --exit-code 0 --severity HIGH,CRITICAL --format table')
        		}
    		}
	}
    }
}
