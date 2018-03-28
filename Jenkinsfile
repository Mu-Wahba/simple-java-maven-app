pipeline {
    agent {
            docker {
                     image 'maven:3-alpine'
                   }
    }
    environment{
	    MY_EMAIL = credentials('my_email')
	    DOCKER_USER = credentials('docker_user')
	    DOCKER_PASS = credentials('docker_pass')
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
        stage('Dockerize_App') {
            steps {
                sh "docker build -t ${DOCKER_USER}/my-app:${BUILD_NUMBER} ."
                sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
                sh "docker push ${DOCKER_USER}/my-app:${BUILD_NUMBER}"
                
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

