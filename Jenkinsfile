pipeline {
    agent any
    tools {nodejs "NPM"}
    environment {
        HOME= '.'
        AWS_ACCOUNT_ID="100682590469"
        AWS_DEFAULT_REGION="us-west-1"
        CLUSTER_NAME="ecs"
        SERVICE_NAME="ecs"
        TASK_DEFINITION_NAME="ecs"
        DESIRED_COUNT="2"
        IMAGE_REPO_NAME="ecs"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
        registryCredential = "co-aws"
    }
   
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        script {
          sh ''
          sh 'npm install'
	        sh 'npm test -- --watchAll=false'
        }
      }
    }
        
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
			docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                    	dockerImage.push()
                	}
         }
        }
      }
      
    stage('Deploy') {
     steps{
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}

