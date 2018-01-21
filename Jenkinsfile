pipeline {
    agent {
            dockerfile true
     }
    environment{
	    MY_EMAIL = credentials('my_email')
	}
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
             }
         }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
		timeout(time:20 , unit:'SECONDS'){
			input message: 'are you sure', ok:'YES!',submitter:'admin'
		}
                sh './scripts/deliver.sh'
            }
            post {
		success {
		    mail ( from: 'Muhammad Wahba',
			   to: "${MY_EMAIL}",
			   subject: "Successs ${JOB_NAME}",
			   body: "Running build: ${BUILD_NUMBER} by executer ${EXECUTOR_NUMBER} , on node: ${NODE_NAME}")
 			}		
        	}	
	}
    }
    post {
	always{
		archiveArtifacts artifacts:'target/*.jar'
        	}
       } 
    options{
	buildDiscarder(logRotator(numToKeepStr:'3'))
	timeout(time:60, unit:'MINUTES')

  }   

}

