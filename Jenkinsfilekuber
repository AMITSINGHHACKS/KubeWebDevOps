
pipeline {
    agent any
    stages {
        stage('checking docker images') {
            steps {
                sh 'docker images -a'
            }
        }
        stage('checking docker containers') {
            steps {
                sh 'docker ps -a'
            }
        }
        stage('remove image') {
            steps {
                sh 'docker rmi -f truthaniket/jenkinsdevops:latest '
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t truthaniket/jenkinsdevops:latest .'
            }
        }
        stage('Deploy Image') {
            steps{
               script {
                  docker.withRegistry( '', 'dockerhub') {
                  sh 'docker push truthaniket/jenkinsdevops:latest'
                }
              }
            }
        }
        stage('Deployment on Kubernetes') {
            steps {
                sh 'kubectl apply -f Kubernetes_deployment.yml'
                sh 'kubectl apply -f servicehtml.yml'
            }
        } 
    }
    post {
    always {
        echo 'Slack Notifications'
        slackSend (
            channel: 'pipeline-notifications',
            message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        )
        mail bcc: '', body: "Hey the project pipeline is running \n *${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} ", cc: 'aniketsingh78698@gmail.com', from: '', replyTo: '', subject: 'Jenkins devops', to: 'as216889@gmail.com'
    }
}
}
