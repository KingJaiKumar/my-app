node
{    
    def buildNumber = BUILD_NUMBER        
    
    stage("GIT CheckOut"){
	    git url:'https://github.com/KingJaiKumar/java-web-app-docker.git',branch: 'master'
    }        
    
    stage("MAVEN Clean Package"){        
	    def mavenHome= tool name: "Maven", type: "maven"        
	    def mavenCMD= "${mavenHome}/bin/mvn"        
	    sh "${mavenCMD} clean package"    
    }
    
    stage("Build DOCKER Image"){        
	    sh "docker build -t kingjai/javawebapp:${buildNumber} ."    
    }
    
    stage("Docker Login and Push"){        
	    withCredentials([string(credentialsId: 'docker_hub_password', variable: 'Dockerpass')]){            
		    sh "docker login -u kingjai -p ${Dockerpass}"        
	    }        
	    sh "docker push kingjai/javawebapp:${buildNumber}"    
    }
	
    stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcat kingjai/javawebapp:${buildNumber}' 
}
