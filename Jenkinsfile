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
    }
}
