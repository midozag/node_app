pipeline{
   agent any
  environment{
     DOCKER_TAG = getDockerTag()
  }
   stages{
    stage('Build Docker image'){
       steps{
         sh "docker build . -t zag2020/nodeapp:${DOCKER_TAG}"
       }
     }
    stage('DockerHub Push'){
       steps{
       withCredentials([string(credentialsId: 'docker-hub-pwd', variable: 'dockerHubPwd')]) {
          sh "docker login -u zag2020 -p ${dockerHubPwd}"
          sh "docker push zag2020/nodeapp:${DOCKER_TAG}"
       }
       }
     }
   }
}
def getDockerTag(){
   def tag = sh script: 'git rev-parse HEAD', returnStdout: true
   return tag
}
