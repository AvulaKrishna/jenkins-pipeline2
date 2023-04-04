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
                           nexusArtifactUploader artifacts: [[artifactId: 'gs-spring-boot-docker', classifier: '', file: 'target/gs-spring-boot-docker-0.1.0.jar', type: 'jar']], credentialsId: '9a12dac2-f814-43f1-994c-7a0db6fc1d1c', groupId: 'org.springframework.boot', nexusUrl: 'http://100.24.53.184:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '0.1.0-SNAPSHOT'
 
                }
        
            }
	  stage('Upload to Nexus') {
            steps {
                sh 'mvn deploy'
    }
}

	  
	  stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
	stage('Build Docker Image') {
    steps {
        script {
            docker.build("my-java-app:${env.BUILD_ID}")
        }
    }
}
        stage('Push Docker Image') {
    steps {
        script {
            docker.withRegistry('http://18.207.197.146:8081.com', 'nexus-credentials') {
                dockerImage.push("${env.BUILD_ID}")
                dockerImage.push("latest")
            }
        }
    }
}
	stage('Deploy to EC2') {
    steps {
        sshagent(['MyEC2Key']) {
            sh '''
                ssh -o StrictHostKeyChecking=no ec2-user@18.207.197.146 "docker pull 18.207.197.146:8000/my-java-app:latest"
                ssh -o StrictHostKeyChecking=no ec2-user@18.207.197.146 "docker stop my-java-app || true && docker rm my-java-app || true"
                ssh -o StrictHostKeyChecking=no ec2-user@18.207.197.146 "docker run -d --name my-java-app -p 8000:8000 18.207.197.146:8000/my-java-app:latest"
            '''
        }
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
		
     post { 
         always { 
            cleanWs()
         }
    }		
}
