pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    stages {
        
        stage('SCM Checkout') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'Git_Pass', url: 'https://github.com/KingJaiKumar/my-app.git']]])
            }
        }
        
         stage('Build') {
            steps {
                sh 'mvn clean install -f pom.xml'
                sh 'mv target/myweb*.war target/newapp.war'
            }
        }
        
       stage('Code Quality') {
            steps {
                    withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar -f pom.xml'
                }
            }
        } 
        
       stage('Build Docker Image') {
            steps {
                sh 'docker build -t kingjai/myweb:0.1 .'
            }
        }
        
        stage('Docker Image Push'){
            steps {
                withCredentials([string(credentialsId: 'DockerPass', variable: 'dpass')]){
                sh "docker login -u kingjai -p ${dpass}"
               }
               sh 'docker push kingjai/myweb:0.1'
            }
        }
        
        stage('Nexus Upload'){
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'myweb', classifier: '', file: 'target/newapp.war', type: 'war']], credentialsId: 'NexusPass', groupId: 'in.java.home', nexusUrl: '52.66.204.55:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '0.0.5'
            }
        }

        stage('Deploy on Kubernetes') {
            steps {
		echo "Deployment"
                //sh 'kubectl apply -f /var/lib/jenkins/workspace/Demo/pod.yaml'
                //sh 'kubectl rollout restart deployment loadbalancer-pod'
            }
        }
	
	 stage('Deploy Approve') {
		 steps {
		 	echo "Taking approval from Manager for Deployment"
			 timeout(time: 7, unit: 'DAYS') {
			 	input message: 'Do you want to deploy?', submitter: 'admin'
			 }
		 }  
	   }
	    
    }
}
