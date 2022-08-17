pipeline {
    agent any 
    tools {
        maven 'Maven'
    }
    stages {
        stage ('Initialize'){
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
        stage ('Deploy') {
            steps{
            sshagent(credentials : ['tomcat']) {
                sh 'ssh -o StrictHostKeyChecking=no root@192.168.1.52 uptime'
                sh 'ssh -v root@192.168.1.52'
                sh 'scp -o StrictHostKeyChecking=no target/*.war root@192.168.1.52:/usr/libexec/tomcat9/webapps/webapps.war'
            }
        }
      }
    
    }
}