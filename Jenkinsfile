pipeline {
    agent any
    tools { 
        maven 'Maven-3.8.6' 
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
	    stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

		stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['deployer']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war deployer@192.168.1.52:/home/tomcat/usr/libexec/tomcat9/webapps/webapp.war'
              }      
           } 
		}         
    }
}
