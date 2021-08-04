pipeline {
  agent any
  tools {nodejs "Node16" }
  environment {
    AWS_ACCOUNT_ID = '118698220025'
    AWS_DEFAULT_REGION = 'eu-west-3'
    IMAGE_REPO_NAME = 'careveo_prod'
    REPOSITORY_URI = '"${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"'
  }
  stages {
    stage('Logging into AWS ECR') {
      steps {
        sh 'aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com'
      }
    }

    stage('Cloning or pulling repo') {
      steps {
        sh 'if cd cicd-demo; then git pull; else git clone https://github.com/fopingn/cicd-demo.git cicd-demo; fi'
      }
    }
    stage('Building image') {
      steps {
        sh 'docker build -t "${IMAGE_REPO_NAME}" .'
      }
    }

    stage('Tagging image') {
      steps {
        sh '''docker tag ${IMAGE_REPO_NAME}:latest ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:latest
                '''
      }
    }

    stage('Pushing image to ECR') {
      steps {
        sh 'docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:latest'
      }
    }
    stage('Saving Archifact') {
            steps {
              archiveArtifacts artifacts: '**', followSymlinks: false
            }
            
        }

  }

}