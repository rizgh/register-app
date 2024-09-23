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
	}
}
