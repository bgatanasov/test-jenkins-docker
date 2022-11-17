pipeline {
     agent any
	
	  tools
    {
       maven "Maven 3.6.3"
    }
 stages {
      stage('checkout') {
           steps {
             
                git branch: 'master', url: 'https://github.com/bgatanasov/test-jenkins-docker.git'
             
          }
        }
	 stage('Execute Maven') {
           steps {
             
                sh 'mvn package'             
          }
        }
        
  stage('Delete all images and containares before build and start new ') {
           
            steps 
			{
                sh "docker container rm tomcat01 tomcat02 --force"
                sh "docker image rm bgatanasov/payara01 --force"	
                sh "docker  image rm tomcat --force"
		sh "docker  image rm payara01 --force"
            }
        }
	 
  stage('Docker Build and Tag') {
           steps {
              
                sh 'docker build -t payara01:latest .' 
                sh 'docker tag payara01 bgatanasov/payara01:latest'
                //sh 'docker tag samplewebapp nikhilnidhi/samplewebapp:$BUILD_NUMBER'
               
          }
        }
     
  stage('Publish image to Docker Hub') {
          
            steps {
        withDockerRegistry([ credentialsId: "dockerHub", url: "" ]) {
          sh  'docker pull bgatanasov/payara01'
        //  sh  'docker push nikhilnidhi/samplewebapp:$BUILD_NUMBER' 
        }
                  
          }
        }
     
      stage('Run Docker container on Jenkins Agent') {
             
            steps 
			{
                sh "docker run --name tomcat01 -d -p 48003:8080 bgatanasov/payara01"
 
            }
        }
 stage('Run Docker container on remote hosts') {
             
            steps {
                sh "docker -H unix:///var/run/docker.sock  run --name tomcat02 -d -p 48004:8080 bgatanasov/payara01"
 
            }
        }
    }
	}
