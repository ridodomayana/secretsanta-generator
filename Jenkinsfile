pipeline {
    agent { label 'Jenkins-Agent' }
    tools{
        jdk 'Java17'
        maven 'Maven3'
    }

    stages {
        stage('git-checkout') {
            steps {
                git 'https://github.com/ridodomayana/secretsanta-generator.git'
            }
        }

        stage('Code-Compile') {
            steps {
               sh "mvn clean compile"
            }
        }
        
        stage('Unit Tests') {
            steps {
               sh "mvn test"
            }
        }

        stage('Sonar Analysis') {
            steps {
               withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                   sh "mvn sonar:sonar"
               }
            }
        }
	stage("Quality Gate"){
	        steps {
		        script{
		            waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
		        }
	        }
	}
		 
        stage('Code-Build') {
            steps {
               sh "mvn clean package"
            }
        }

        stage('Docker Build') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'dockerhub') {
                    sh "docker build -t  santa1234 . "
                   }
               }
            }
        }

        stage('Docker Push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'dockerhub') {
                    sh "docker tag santa1234 rido4good/santa1234:latest"
                    sh "docker push rido4good/santa1234:latest"
                   }
               }
            }
        }
		
    }
}
