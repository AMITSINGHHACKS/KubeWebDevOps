
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
        stage('Azure Login') {
            steps {
                script {
                    // Use Azure CLI to log in
                    withCredentials([azureServicePrincipal(credentialsId: 'Azure')]) {
                        withEnv(["AZURE_SUBSCRIPTION_ID=${AZURE_CREDENTIALS.subscriptionId}", "AZURE_TENANT_ID=${AZURE_CREDENTIALS.tenant}", "AZURE_CLIENT_ID=${AZURE_CREDENTIALS.clientId}", "AZURE_CLIENT_SECRET=${AZURE_CREDENTIALS.secret}"]) {
                            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
                            // Add more Azure CLI commands as needed
                        // Add more Azure CLI commands as needed
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
