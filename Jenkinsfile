pipeline {
    agent any
    
   
    stages {
	
	
        
        
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/dev']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jyogithub', url: 'https://github.com/jyothishkshatri/aws_codebuild_codedeploy_nodeJs_demo.git']]])     
            }
        }
		stage('Reading prop file') {
		
		steps {
         script {
       def props = readProperties file: 'env.properties'
        env.AWS_ACCOUNT_ID = props.AWS_ACCOUNT_ID
        env.AWS_DEFAULT_REGION = props.AWS_DEFAULT_REGION
        env.IMAGE_REPO_NAME = props.IMAGE_REPO_NAME
        env.IMAGE_TAG = props.IMAGE_TAG
        env.REPOSITORY_URI = props.REPOSITORY_URI
		
		sh "echo aws account ID is $AWS_ACCOUNT_ID"
		sh "echo aws region is $AWS_DEFAULT_REGION"
		sh "echo image tag is $IMAGE_TAG"
		sh "echo repo uri is $REPOSITORY_URI"
		sh "echo repo name is ${env.GIT_URL}"
		sh "echo branch name is ${env.BRANCH_NAME}"
		sh "echo branch git name is ${env.GIT_BRANCH}"

         }
		
		}

}

 stage('Logging into AWS ECR') {
            steps {
                script {
                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
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
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
         }
        }
      }
    }
}
