pipeline {
    agent any

    stages {
    
        stage("CI/CD: Warm Up"){
            steps {
                script {
                    sh "java -version"

                }
            }
        }
		
		stage("Checkout from SCM"){
            steps {
                script {
                    sh "git clone https://github.com/theopskart22/jenkins-pipeline2.git"				
                }
            }
        }
	

        stage('Build ') {
            steps {
            script {
                sh "cd ${WORKSPACE}/jenkins-pipeline2 "
                sh "cd jenkins-pipeline2 && mvn clean install "
            }
        }
        }
	stage ('Code Quality') {
        steps {
            withSonarQubeEnv('SonarQube') {
            sh 'mvn -f pom.xml sonar:sonar'
            }
      }
    }
	stage ('Nexus upload') {
                steps {
                           nexusArtifactUploader artifacts: [[artifactId: 'gs-spring-boot-docker', classifier: '', file: 'target/gs-spring-boot-docker-0.1.0.jar', type: 'jar']], credentialsId: '9a12dac2-f814-43f1-994c-7a0db6fc1d1c', groupId: 'org.springframework.boot', nexusUrl: 'http://54.205.82.94:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '0.1.0-SNAPSHOT'
 
                }
        
            }

        stage('Verify ') {
            steps {
            script {
                sh "cd ${WORKSPACE}/jenkins-pipeline2 "
                sh "ls -lhrt jenkins-pipeline2 "
            }
        }
        }        
        
		}
     	stage('Deploy') {
            steps {
                // Copy the artifact to EC2 instance
                sh 'scp target/my-app.jar ec2-user@<http://3.86.95.111>:~/my-app.jar' // Replace with your artifact details and EC2 public IP

                // SSH into EC2 instance and run Docker image
                sh 'ssh ec2-user@<http://3.86.95.111> "docker stop my-app; docker rm my-app; docker pull my-docker-image; docker run -d -p 80:8080 --name my-app my-docker-image"' // Replace with your Docker image details and EC2 public IP
            }
        }
    }
}
		
     post { 
         always { 
            cleanWs()
         }
    }		
}
