pipeline {
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
                  sh 'hadolint Dockerfile'
              }
         }

      
        //  stage('Upload to AWS') {
        //       steps {
        //           withAWS(region:'us-east-1',credentials:'talismanic-cfit-serverless-6621-dev') {
        //           sh 'echo "Uploading content with AWS creds"'
        //               s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'talismanic-cfit-serverless-6621-dev')
        //           }
        //       }
        //  }
     }
}