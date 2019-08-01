node {
label 'windows' 
    withMaven(maven:'maven') {

        stage('Checkout') {
            git url: 'https://github.com/dhananjayakTpm/AtoBeMailTest.git', credentialsId: 'master', branch: 'master'
        }

		stage('Build') {
            bat 'mvn clean package shade:shade'
            def pom = readMavenPom file:'pom.xml'
            env.version = pom.version
        }

        stage('Image') {
                bat 'docker stop restassuredMail || exit 0'
				bat 'docker rm restassuredMail || exit 0'
                cmd = "docker rmi restassuredMail:${env.version} || exit 0"
                bat cmd
                bat "docker build -t restassuredMail:${env.version} ."
            
        }

        stage ('Run') {

       		 print "${params}"
        	 if ("${params.modes}" == "DRY_RUN") {
       			 bat "docker run -p 8081:8081 -h restassuredMail --name restassuredMail --net host -m=500m restassuredMail:${env.version} DRY_RUN"
      	     }
      	     else if("${params.modes}" == "RUN") {
	  	 	 	 bat "docker run -p 8081:8081 -h restassuredMail --name restassuredMail --net host -m=500m restassuredMail:${env.version} RUN"
      	     }
      	     else if("${params.modes}" == "FULL_RUN") {
	  	 		 bat "docker run -p 8081:8081 -h restassuredMail --name restassuredMail --net host -m=500m restassuredMail:${env.version} FULL_RUN"
      	     }
          
          	  
          
        }

    }

}
