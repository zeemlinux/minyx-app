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
                sh 'scp -o StrictHostKeyChecking=no target/*.war root@192.168.1.51:/opt/tomcat/webapps/webapp.war'
              }      
           } 
	   }  
           
        stage ('Port Scan') {
		    steps {
			sh 'rm nmap* || true'
			sh 'docker run --rm -v "$(pwd)":/data uzyexe/nmap -sS -sV -oX nmap 192.168.1.50'
			sh 'cat nmap'
		    }
	    }
	    
	    stage ('DAST') {
		  
		    	steps {
			    sshagent(['zap']) {
				    sh 'ssh -o StrictHostKeyChecking=no root@192.168.1.50 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://192.168.1.51:8080/webapp/" || true'
			    }
			}
		}
        
		  
    }
}
