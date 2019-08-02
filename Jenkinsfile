
node (label:'windows'){

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
                bat 'docker stop restassuredmail || exit 0'
				bat 'docker rm restassuredmail || exit 0'
                cmd = "docker rmi restassuredmail:${env.version} || exit 0"
                bat cmd
                bat "docker build -t restassuredmail:${env.version} ."
            
        }

        stage ('Run') {

       		 print "${params}"
        	 if ("${params.modes}" == "DRY_RUN") {
       			 bat "docker run -p 8081:8081 -h restassuredmail --name restassuredmail --net host -m=500m restassuredmail:${env.version} DRY_RUN"
      	     }
      	     else if("${params.modes}" == "RUN") {
	  	 	 	 bat "docker run -p 8081:8081 -h restassuredmail --name restassuredmail --net host -m=500m restassuredmail:${env.version} RUN"
      	     }
      	     else if("${params.modes}" == "FULL_RUN") {
	  	 		 bat "docker run -p 8081:8081 -h restassuredmail --name restassuredmail --net host -m=500m restassuredmail:${env.version} FULL_RUN"
      	     }
		
           bat "docker container export -o restassuredmail.zip restassuredmail"
		
	 unzip( zipFile: env.WORKSPACE+'/restassuredmail.zip')
         env.ForEmailPlugin = env.WORKSPACE
        emailext mimeType: 'text/html',
	attachLog :true,
	compressLog : true,
        body: '${FILE, path="test-output/emailable-report.html"}',
        subject: currentBuild.currentResult + " : " + env.JOB_NAME,
        to: 'dhananjaya.k@thinkpalm.com'
	  
          
        }

    }

}
