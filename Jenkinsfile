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
                    url: 'https://github.com/bhavikpalia/az_function.git',
                    credentialsId: 'github-PAT'
            }
        }

        stage('Install .NET SDK') {
            steps {
                bat '''
                    powershell -command "Invoke-WebRequest https://dot.net/v1/dotnet-install.ps1 -OutFile dotnet-install.ps1"
                    powershell -command "./dotnet-install.ps1 -Channel 6.0"
                '''
            }
        }

        stage('Restore Dependencies') {
            steps {
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build --configuration Release'
            }
        }

        stage('Publish') {
            steps {
                bat 'dotnet publish --configuration Release --output publish_output'
            }
        }

        stage('Deploy to Azure') {
            steps {
                bat '''
                    az functionapp deployment source config-zip -g %RESOURCE_GROUP% -n %FUNCTION_APP_NAME% --src publish_output.zip
                '''
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
