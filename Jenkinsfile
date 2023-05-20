pipeline {
	agent any
	
	tools {
 		maven "maven"
	}
	 environment {
        registryCredential = 'ecr:ap-south-1:awscred'
        appRegistry = "854860754449.dkr.ecr.ap-south-1.amazonaws.com/jenkins"
        registry = "https://854860754449.dkr.ecr.ap-south-1.amazonaws.com"
        cluster = "demo"
        service = "demo"
    }
	
	stages {
		stage ('Fetching the code from git') {
		   steps {
			git branch: 'master', url: 'https://github.com/st662/JenP.git'
		   }
		}
        stage ('Unit Test') {
		    steps {
			sh 'mvn test'
		    }
		}
		stage ('Build with Docker') {
		    steps {
			  script {
                dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", ".")
              }
		   }
		}
	    stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( registry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
        }
        stage('Deploy to ecs') {
          steps {
        withAWS(credentials: 'awscred', region: 'ap-south-1') {
          sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
        }
      }
     }
  }
}
