pipeline {
    environment {
    dockerregistry = 'https://registry.hub.docker.com'
    dockerhuburl = 'novanovn/testnodeapp'
    githuburl = 'novanovn/simple-node-js-react-npm-app'
    dockerhubcrd = '94992ee7-67a1-4a48-8086-d9a1d2d1ddb3'
	CI = 'true'
    }
    agent any
    
	stages {
        stage('Test') {
            agent {
                docker { image 'node:6-alpine'
				args '-p 3001:3000'}
            }
            steps {
                sh 'npm install'
				sh './jenkins/scripts/test.sh'
				sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
        stage('Clone git repo') {
            steps {
               git 'https://github.com/' + githuburl
            }
        }
		stage('Build image') {
           steps{
              script {
               dockerImage = docker.build(dockerhuburl + ":$BUILD_NUMBER")
               }
            }
        }
        stage('Push Image') {
            steps{
             script {
               docker.withRegistry( "" ) {
               dockerImage.push()
               dockerImage.push('latest')
                }
              }
            }
        }
    
        stage('Remove Unused docker image') {
            steps{
              sh "docker rmi $dockerhuburl:$BUILD_NUMBER"
             
            }
        }

    }
}
