pipeline {
    agent any
    
    tools {
        dockerTool 'docker'
        // org.jenkinsci.plugins.docker.commons.tools.DockerTool
        
        
    }



    stages {

        stage ('Checkout'){
            steps{

                echo "checking out the code"
                sh "ls"
            }
        }

        

       stage('Build and Push'){
            steps{
                echo 'deploying application updates....'

                script{
                    docker.withRegistry(
                        'https://558908427467.dkr.ecr.us-east-1.amazonaws.com/cloudperis',
                        'ecr:us-east-1:Jenkins-aws') {
                        def myImage = docker.build('cloudperis')
                        myImage.push('latest')
                        }

                }
                

            }
        }

        stage('Deploy-Dev'){
            steps{
                echo 'deploying application updates....'
                withCredentials([[
                      $class: 'AmazonWebServicesCredentialsBinding',
                      credentialsId: "Jenkins-aws",
                      accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                      secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {

                          
                          sh "aws ecs update-service --service my-service --force-new-deployment"

                      }


            }
        }


        
    }
    post{
        success{
           slackSend channel: 'jenkins-notifications', color: '439FE0', message: "SUCCESS ${currentBuild.fullDisplayName} at ${currentBuild.durationString[0..-13]}" 
        }
        failure{
            slackSend channel: 'jenkins-notifications', color: '#fc0303', message: "FAILURE ${currentBuild.fullDisplayName} at ${currentBuild.durationString[0..-13]}"
        }
    }
}