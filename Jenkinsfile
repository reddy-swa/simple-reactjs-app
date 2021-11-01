pipeline {
    agent any
    stages {
        stage('Git'){
            steps{
             //   git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/reddy-swa/node-jenkins-demo.git'
                git credentialsId: 'GitHub', url: 'https://github.com/reddy-swa/simple-reactjs-app.git'
            }
        }
        stage('Build frontend application') {
            agent {
                docker {image 'node:14-alpine' }
            }
            steps {
                sh 'node --version'
            }
        }
        stage('Build docker image'){
            steps{
                sh 'docker build -t reddyswa/nodeimage:${BUILD_NUMBER} . '
            }
        }
        stage('Push image to dockerhub'){
            steps{
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhubPwd')]) {
                    sh "docker login -u reddyswa -p ${dockerhubpwd}"
                    sh 'docker push reddyswa/nodeimage:${BUILD_NUMBER}'
                }
            }
        }
        stage('Deployment stage'){
            steps{
                echo dockerRun ='docker run -p 3001:3000 -d reddyswa/nodeimage:${BUILD_NUMBER}'
                sshagent(['dev-server']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.36.216 ${dockerRun}"
                }
            }
        }
    }
    post {
        always{
            cleanWs()
        } 
    }
}
