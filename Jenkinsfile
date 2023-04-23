pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:adarsh'
       appRegistry = "341656755951.dkr.ecr.ap-northeast-1.amazonaws.com/capstone_project"
       capstoneRegistry = "https://434954100083.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "adarsh"
       service = "adarsh"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/008adarshshukla/adarsh'
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
            withAWS(credentials: 'adarsh', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
