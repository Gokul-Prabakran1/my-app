node{
   stage('SCM Checkout'){
     git 'https://github.com/Gokul-Prabakran1/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven5', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
  
   stage('Build Docker Imager'){
   sh 'docker build -t gokul72/myweb:0.0.2 .' 
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u gokul72 -p ${dockerPassword}" 
    }
   sh 'docker push gokul72/myweb:0.0.2' 
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 3.110.161.21:8083"
   sh "docker tag gokul72/myweb:0.0.2 3.110.161.21:8083/gokul:1.0.0" 
   sh 'docker push 3.110.161.21:8083/gokul:1.0.0' 
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f webcontainer' 
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name webcontainer gokul72/myweb:0.0.2'  
   }
}
}
