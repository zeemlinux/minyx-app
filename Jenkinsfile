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
		sh 'docker pull gesellix/trufflehog'
		sh 'docker run -t gesellix/trufflehog --json https://github.com/zeemlinux/minyx-app.git > trufflehog'
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
