pipeline {
    environment {
        registry = "talismanic/capstone-restapi"
        registryCredential = 'dockerhub'
        dockerImage = ''
        }
    
    agent any

     stages {
         stage('Build') {
             steps {
                 sh 'echo "Hello World"'
                 sh '''
                     echo "Multiline shell steps works too"
                     ls -lah
                 '''
             }
         }
         stage('Lint Dockerfile') {
              steps {
                  sh '/home/linuxbrew/.linuxbrew/bin/hadolint Dockerfile'
              }
         }
         stage('Building image') {
             steps{
                 script {
                      dockerImage = docker.build registry + ":latest"
                     }
                }
            }
        

        stage('Push Image to Dockerhub') {
              steps{
                      script {
                                docker.withRegistry( '', registryCredential ) 
                                {
                                    dockerImage.push()
                                 }
                            }
                    }
                }
         
         stage('Cluster Check') {
             steps {
                 sh 'echo "Checking EKS Cluster"'
                 sh '''
                     /usr/local/bin/eksctl get cluster
                 '''
             }
         }

            
      
     }
}
