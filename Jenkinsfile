pipeline {
    environment {
        registry = "talismanic/capstone-restapi"
        registryCredential = 'dockerhub'
        dockerImage = ''
        awsCred = 'aws_creds'
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
         
         stage('Application Deployment') {
             steps {
                withAWS(region:'us-east-1', credentials:'talismanic-cfit-serverless-6621-dev') {
                    sh '''
                    echo "Checking whether custer exists"
                    clusterName=$(aws eks list-clusters | jq -r '.clusters[0]')
                    if [ $clusterName == "MyCluster01" ]
                       then
                          echo "Cluster Exists"
                          cd k8s
                          kubectl apply -f .                      
                    fi

                    '''
                    }
             }
         }
     }
}
