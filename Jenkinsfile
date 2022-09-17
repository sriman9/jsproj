agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t sriman99/js_image:${DOCKER_TAG} "
          }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u sriman99 -p ${dockerHubPwd}"
                    sh "docker push sriman99/js_image:${DOCKER_TAG}"
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['kops-machine']) {
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@52.66.70.61:/home/ec2-user/"
                    script{
                        try{
                            sh "ssh ec2-user@52.66.70.61 kubectl apply -f ."
                        }catch(error){
                            sh "ssh ec2-user@52.66.70.61 kubectl create -f ."
                           }
                       
                      }
                     }
               }
        }
