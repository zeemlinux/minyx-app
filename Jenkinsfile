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
        stage ('Deploy-to-Tomcat') {
            steps {
           sshagent(['tomcat2']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war root@192.168.1.52:/usr/libexec/tomcat9/webapps/webapp.war'
              }      
           }       
       }
    
    }
}