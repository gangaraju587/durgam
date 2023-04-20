pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                git url: 'https://github.com/gangaraju587/hardeep.git'    
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
            when {
                expression {
                    currentBuild.result == 'SUCCESS'
                }
            }
            steps {
                script {
                    sshagent(['my-ssh-key']) {
                        sh "gcloud compute ssh --zone "us-west4-b" "instance-2" --project "hardeep-poc" 'mkdir test && cd test && git pull && cd hardeep && mvn clean package && docker build -t webapp:${buildNumber} . && docker run -d -p 8080:8080 --entrypoint=\"/bin/sh\" webapp:${buildNumber} -c \"sh /usr/local/tomcat/bin/startup.sh;while true; do echo hello; sleep 10;done\"'"
                    }
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
