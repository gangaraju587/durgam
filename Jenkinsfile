pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                git url: 'https://github.com/gangaraju587/durgam.git'    
		            echo "Code Checked-out Successfully!!";
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn package'    
		            echo "Maven Package Goal Executed Successfully!";
            }
        }
        
      

	stage('SonarQube analysis') {
            steps {
		// Change this as per your Jenkins Configuration
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn package sonar:sonar'
                }
            }
        }

	stage("Quality gate") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
	stage('SSH into server') {
            /*when {
                expression {
                    currentBuild.result == 'SUCCESS'
                }
            }*/
            steps {
		    script {
				    sh """
				    whoami
				    pwd
				    mvn clean package
				    sudo docker build -t webapp .
				    sudo docker run -d -p 8081:8080 --entrypoint="/bin/sh" webapp -c "sh /usr/local/tomcat/bin/startup.sh;while true; do echo hello; sleep 10;done"
				    sudo docker ps
				    """
                    }
                }
            }
        }
        
     
    post {
        
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
    
    }
}
