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
            /*when {
                expression {
                    currentBuild.result == 'SUCCESS'
                }
            }*/
            steps {
                script {
		sh """
		    ssh -i ~/.ssh/jenkins jenkins@34.125.22.150 << EOF
		    cd hardeep
		    git pull
		    mvn clean package
		    for i in $(docker ps | awk $'{print $1}'); do  docker stop $i; done
		    docker build -t webapp:${BUILD_NUMBER} .
		    docker run -d -p 8080:8080 --entrypoint="/bin/sh" mywebapp:${BUILD_NUMBER} -c "sh /usr/local/tomcat/bin/startup.sh;while true; do echo hello; sleep 10;done"
                    EOF
                """
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
