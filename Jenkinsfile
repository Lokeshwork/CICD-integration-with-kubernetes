pipeline {
  agent {label 'agent3'}
	environment {
	AWS_ACCOUNT_ID="115069646213"
	AWS_DEFAULT_REGION="us-east-1"
	IMAGE_REPO_NAME="simple-time-app"
	IMAGE_TAG="lokeshj/nodeapp"
	REPO_URL = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/simple-time-app"
	}

  agent any

  stages {

    stage('Git Checkout') {
      steps {
        git 'https://github.com/Lokeshwork/nodeapp_test.git'
      }
    }

	stage('dockerbuild'){ 
	steps{
			script{
					sh "docker build -t ${REPO_URL}:${IMAGE_TAG} ."
				}
		}
	}

stage('logging to AWS ECR'){
		steps{
			script {
					sh """aws ecr get-login-password \
			--region ${AWS_DEFAULT_REGION} \
			| docker login \
			--username AWS \
			--password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
			}
		}
	}
	
	stage('ECR push') {
		steps{
			script {
			sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
			}
		}
	}

    stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")
        }
      }
    }

  }

}
