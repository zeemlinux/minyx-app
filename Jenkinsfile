pipeline {
    agent any
    tools { 
        maven 'maven' 
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
		stage ('Check-Git-Secrets') {
		    steps {
	        sh 'rm trufflehog || true'
		sh 'docker pull trufflesecurity/trufflehog'
		sh 'docker run -it -v "$PWD:/pwd" ghcr.io/trufflesecurity/trufflehog:latest --json github --repo https://github.com/zeemlinux/minyx-app.git --debug > trufflehog'
		sh 'cat trufflehog'
	    }
	 }

	    stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
		stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@44.210.126.209:/opt/tomcat/webapps/webapp.war'
              }      
           } 
	    }         
    }
}
