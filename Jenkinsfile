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
                    sh "git clone https://github.com/AvulaKrishna/jenkins-pipeline2.git "				
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
                           nexusArtifactUploader artifacts: [[artifactId: 'gs-spring-boot-docker', classifier: '', file: 'target/gs-spring-boot-docker-0.1.0.jar', type: 'jar']], credentialsId: '7fe36265-930a-45f6-9a6a-43ea0f3d1d12', groupId: 'org.springframework.boot', nexusUrl: 'http://54.221.53.183:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '0.1.0-SNAPSHOT'
 
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
         stage ('Publish to ECR') {
      steps {
        //sh 'aws ecr-public get-login-password --region eu-west-2 | docker login --username AWS --password-stdin public.ecr.aws/t7e2c6o4'
        //withAWS(credentials: 'sam-jenkins-demo-credentials', region: 'eu-west-2') {
         withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
          sh 'docker login -u AWS -p $(    stage ('Publish to ECR') {
      steps {
        //sh 'aws ecr-public get-login-password --region eu-west-2 | docker login --username AWS --password-stdin public.ecr.aws/t7e2c6o4'
        //withAWS(credentials: 'sam-jenkins-demo-credentials', region: 'eu-west-2') {
         withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
          sh 'docker login -u AWS -p $(aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 090356033263.dkr.ecr.us-east-1.amazonaws.com
          sh 'docker build -t ecr-demoimg .'
          sh 'docker tag ecr-demoimg:latest 090356033263.dkr.ecr.us-east-1.amazonaws.com/ecr-demoimg:latest'
          sh 'docker push 090356033263.dkr.ecr.us-east-1.amazonaws.com/ecr-demoimg:latest'
         }
       }
    }
  }
}
      stages {
    stage('Build') {
      steps {
        // Build your Docker image
        sh 'docker build -t your-image-name .'
      }
    }
    stage('Push') {
      steps {
        // Authenticate with AWS ECR
        withCredentials([awsEcr(credentialsId: 'AWSInforma', region: 'us-east-1')]) {
          // Tag the Docker image
          sh 'docker tag your-image-name:latest AWSInforma.dkr.ecr.us-east-1.amazonaws.com/your-repo-name:latest'
          // Push the Docker image to ECR
          sh 'docker push AWSInforma.dkr.ecr.us-east-1.amazonaws.com/your-repo-name:latest'
        }
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
