pipeline {

    agent any

    options {
        buildDiscarder logRotator( 
                    daysToKeepStr: '16', 
                    numToKeepStr: '10'
            )
    }

    stages {

        stage('Testing Feature Branch') {
            when {
                branch 'feature'
            }
            steps {
              cleanWs()
              git branch: 'feature', url:'https://github.com/appychipDevOps/CICD-demo.git'
              sh '''
              npm install
              npm test test.js
              '''
            }
        }
        
        stage('Deploy on Staging server') {
            when {
                branch 'develop'
            }
            steps {
                sshagent(['stage'])  {
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.226.35.1 /home/ubuntu/deployStaging.sh' 	
                }
            }
        }

        stage('Testing Develop') {
            when {
                branch 'develop'
            }
          steps {
                script{
                sleep 5
                def responseCode = sh(script: 'curl -I http://54.226.35.1:4000/', returnStdout: true).trim()
                 def responseCodeNumber = responseCode.substring(9, 12)
                    echo 'The response code is: ' + responseCodeNumber
                  if (responseCodeNumber == '200') {
                    echo 'The app is deployed'
                  } else {
                    error('App is not deployed')
                  }
            }
          }
        }
    

    }   
}