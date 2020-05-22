pipeline {
    environment {
        registry = "talismanic/capstone-restapi"
        registryCredential = 'dockerhub'
        dockerImage = ''
        awsCred = 'aws_creds'
        }
    
    agent any

     stages {
         stage('Starting Pipeline') {
             steps {
                 sh 'echo "Starting pipeline of Udacity Capstone Project"'
                 sh '''
                     echo "Lets check the working directory pulled from Github"
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
                    echo "Checking whether eks cluster exists"
                    clusterName=$(aws eks list-clusters | jq -r '.clusters[0]')
                    if [ $clusterName == "MyCluster01" ]
                       then
                          echo "Cluster Exists"
                          cd k8s
                          kubectl apply -f .

                    else
                        echo "Cluster does not exist, please create the cluster first"                      
                    fi
                    '''
                    }
             }
         }

        stage('Checking Kubernetes Resources') {
             steps {
                 sh 'kubectl get all'
             }
         }
     }
}
