node{
   stage('SCM Checkout'){
     git 'https://github.com/Anantharajaru/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Imager'){
   sh 'docker build -t anantharajaru/myfirst:0.0.3 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u anantharajaru -p ${dockerPassword}"
    }
   sh 'docker push anantharajaru/myfirst:0.0.3'
   }
   stage('Nexus Image Push'){
   withCredentials([string(credentialsId: 'nexusPass', variable: 'nexusPassword')]) {
   sh "docker login -u admin -p ${nexusPassword} 3.95.255.4:8083"
   sh "docker tag anantharajaru/myfirst:0.0.3 3.95.255.4:8083/ananth:1.0.0"
   sh 'docker push 3.95.255.4:8083/ananth:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcat'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcat anantharajaru/myfirst:0.0.3' 
   }
}
}

