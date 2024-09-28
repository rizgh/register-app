/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent { label 'slave' }
    tools {
        jdk 'java17'
        maven 'maven3'
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
        stage('quality gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                }
            }
        }
    }
}
