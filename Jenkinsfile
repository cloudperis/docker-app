pipeline {
    agent any
    
    tools {
        dockerTool 'docker'
        //org.jenkinsci.plugins.docker.commons.tools.DockerTool
        
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
                withCredentials([[
                      $class: 'AmazonWebServicesCredentialsBinding',
                      credentialsId: "Jenkins-aws",
                      accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                      secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {

                          
                         
                          sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 558908427467.dkr.ecr.us-east-1.amazonaws.com"
                          sh "docker build -t cloudperis ."
                          sh "docker tag cloudperis:latest 558908427467.dkr.ecr.us-east-1.amazonaws.com/cloudperis:latest"
                          sh "docker push 558908427467.dkr.ecr.us-east-1.amazonaws.com/cloudperis:latest"

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