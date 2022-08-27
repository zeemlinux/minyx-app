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
           
        stage ('Port Scan') {
		    steps {
			sh 'rm nmap* || true'
			sh 'docker run --rm -v "$(pwd)":/data uzyexe/nmap -sS -sV -oX nmap 192.168.1.50'
			sh 'cat nmap'
		    }
	    }
      
	    
	    stage ('DAST') {
		  
		    	steps {
			    sshagent(['jenkins']) {
				    sh 'ssh -o StrictHostKeyChecking=no root@192.168.1.50 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://192.168.1.51:8080/webapp/" || true'
			    }
			}
		}
        stage ('SAST') {
		steps {
		withSonarQubeEnv('sonar') {
			sh 'mvn sonar:sonar'
			sh 'cat target/sonar/report-task.txt'
		       }
		}
	}
	    stage ('Nikto Scan') {
		    steps {
			sh 'rm nikto-output.xml || true'
			sh 'docker pull secfigo/nikto:latest'
			sh 'docker run --user $(id -u):$(id -g) --rm -v $(pwd):/report -i secfigo/nikto:latest -h 192.168.1.50 -p 8080 -output /report/nikto-output.xml'
			sh 'cat nikto-output.xml'   
		    }
	    }
	   stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war root@192.168.1.51:/opt/tomcat/webapps/webapp.war'
              }      
           } 
       }     
		stage ('Upload Reports to Defect Dojo') {
		    steps {
			sh 'pip install requests'
			sh 'wget https://raw.githubusercontent.com/devopssecure/webapp/master/upload-results.py'
			sh 'chmod +x upload-results.py'
			sh 'python upload-results.py --host 192.168.1.51 --api_key a13b6e9af412eb32dfe2771b264164d9fc3a8297 --engagement_id 4 --result_file trufflehog --username admin --scanner "SSL Labs Scan"'
			sh 'python upload-results.py --host 192.168.1.51 --api_key a13b6e9af412eb32dfe2771b264164d9fc3a8297 --engagement_id 4 --result_file /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml --username admin --scanner "Dependency Check Scan"'
			sh 'python upload-results.py --host 192.168.1.51 --api_key a13b6e9af412eb32dfe2771b264164d9fc3a8297 --engagement_id 4 --result_file nmap --username admin --scanner "Nmap Scan"'
			sh 'python upload-results.py --host 192.168.1.51 --api_key a13b6e9af412eb32dfe2771b264164d9fc3a8297 --engagement_id 4 --result_file nikto-output.xml --username admin'
			    
		    }
	    }  

    }
}
