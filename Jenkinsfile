
pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS = credentials('Azure')
    }
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
        stage('Build') {
            steps {
                sh 'docker build -t truthaniket/kubeweb:latest .'
            }
        }
        stage('Deploy Image') {
            steps{
               script {
                  docker.withRegistry( '', 'dockerhub') {
                  sh 'docker push truthaniket/kubeweb:latest'
                }
              }
            }
        }
        stage('Azure Login') {
            steps {
                script {
                    // Use Azure CLI to log in
                    withCredentials([azureServicePrincipal(credentialsId: 'Azure', variable: 'AZURE_CREDENTIALS')]) {
                        sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
                           
                        // Add more Azure CLI commands as needed
                        // Add more Azure CLI commands as needed
                    }
                }
            }
        }
        stage('Deployment on Kubernetes') {
            steps {
                sh 'kubectl get pods'
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
