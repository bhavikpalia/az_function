pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = credentials('github-PAT')
        AZURE_CREDENTIALS = credentials('Azure_SP')
        FUNCTION_APP_NAME = 'httptriggeredfuctionapp'
        RESOURCE_GROUP = 'httptriggeredfuctionapp_group'
        DOTNET_VERSION = '6.0'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/bhavikpalia/AZ_fucntionApp.git',
                    credentialsId: 'github-PAT'
            }
        }

        stage('Restore Dependencies') {
            steps {
                script {
                    dotnetSdk(dotnetVersion: "${env.DOTNET_VERSION}")
                }
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --configuration Release'
            }
        }

        stage('Publish') {
            steps {
                sh 'dotnet publish --configuration Release --output publish_output'
            }
        }

        stage('Deploy to Azure') {
            steps {
                azureCLI {
                    script {
                        sh """
                            az functionapp deployment source config-zip -g $RESOURCE_GROUP -n $FUNCTION_APP_NAME --src publish_output.zip
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
