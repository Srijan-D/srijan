pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:srijan'
       appRegistry = "200673010477.dkr.ecr.ap-south-1.amazonaws.com/srijan"
       capstoneRegistry = "https://200673010477.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "srijan"
       service = "srijan"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Srijan-D/srijan'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'srijan', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
