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
	    stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
		stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@44.210.126.209:/opt/tomcat/webapps/webapps.war'
              }      
           } 
		}         
    }
}
