// DECLARATIVE APPROACH
pipeline {
	agent any // Where the build is going to run
	environment { // Set variables for Jenkinsfile to use in tasks
		// We configure 'myDocker' & 'myMaven' in 'Manage Jenkins' -> 'Global Tool Configuration'
		// See 184
		dockerHome = tool 'myDocker'
		mavenHome = tool 'myMaven'
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH" // Add folders to PATH
	}

	stages {
		stage('Build') {
			steps {
				sh 'mvn --version'
				sh 'docker version'
				echo "Build"
				echo "PATH - $PATH" // /opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
				echo "env.PATH - $env.PATH"  // /opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
				echo "BUILD_NUMBER - $env.BUILD_NUMBER" // 16
				echo "BUILD_ID - $env.BUILD_ID" // 16
				echo "JOB_NAME - $env.JOB_NAME" // jenkins-devops-microservice-pipeline
				echo "BUILD_TAG - $env.BUILD_TAG" // jenkins-jenkins-devops-microservice-pipeline-16
				echo "BUILD_URL - $env.BUILD_URL" // http://localhost:8081/job/jenkins-devops-microservice-pipeline/16/
			}
		}

		stage('Compile') {
			steps {
				sh "mvn clean compile"
			}
		}

		stage('Test') {
			steps {
				echo "Test"
				sh "mvn test"
			}
		}

		stage('Integration Test') {
			steps {
				echo "Integration test"
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}

		stage('Package') {
			steps {
				// Create JAR file
				sh "mvn package -DskipTests"
			}
		}

		stage('Build Docker Image') {
			steps {
				script {
					dockerImage = docker.build("redi2000/currency-exchange-devops:${env.BUILD_TAG}")
				}

			}
		}

		stage('Push Docker Image') {
			steps {
				script {
					// 'dockerhub' - credential ID from dockerhub set in Jenkins admin
					docker.withRegistry('', 'dockerhub') {
						dockerImage.push();
						dockerImage.push('latest');
					}
				}
			}
		}
	}

	post { // Part that is conditionally executed after "STAGES
		always {
			echo 'Im awesome. I run always'
		}
		success {
			echo 'I run when you are successful'
		}
		failure {
			echo 'I run when you fail'
		}
	}
}