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
      stage('Deploy to k8s'){
         steps{
           sh "chmod +x changeTag.sh"
           sh "./changeTag.sh ${DOCKER_TAG}"
           sshagent(['kops-machine']) {
                sh "scp -o StrictHostKeyChecking=no node-app-pod.yml services.yml ec2-user@18.188.131.191:/home/ec2-user/"
            }
            script{
               try{
                  sh "ssh ec2-user@18.188.131.191 kubectl apply -f"
               }
               catch(error){
                 sh "ssh ec2-user@18.188.131.191 kubectl create -f"
               }
            }
         }
      }
   }
}
def getDockerTag(){
   def tag = sh script: 'git rev-parse HEAD', returnStdout: true
   return tag
}
