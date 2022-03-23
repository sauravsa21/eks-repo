pipeline {
agent any
   environment {
       AWS_ACCOUNT_ID="692851696394"
       AWS_DEFAULT_REGION="us-east-1"
       IMAGE_REPO_NAME="test"
       REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
       IMAGE_TAG1="${env.BUILD_NUMBER}"
       IMAGE_TAG2="${env.GIT_COMMIT}"
       IMAGE_TAG3="latest"
       TASK_DEF="test-task:5"
 }
stages {
   stage ("checkout") {
       steps {
           checkout scm
       }
   }
  stage ("building image") {
      steps {
          sh 'docker build -t test . --network=host --no-cache'
      }
  }
    stage('login and pushing') {
     steps {
           sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 692851696394.dkr.ecr.us-east-1.amazonaws.com"
           sh "docker tag ${IMAGE_REPO_NAME}:latest ${REPOSITORY_URI}:$IMAGE_TAG1"
           sh "docker tag ${IMAGE_REPO_NAME}:latest ${REPOSITORY_URI}:$IMAGE_TAG2"
           sh "docker tag ${IMAGE_REPO_NAME}:latest ${REPOSITORY_URI}"
           sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG1}"
           sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG2}"
           sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG3}"
           }   
    }
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'deploymentpipeline', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
   
}
}
