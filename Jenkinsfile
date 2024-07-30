pipeline {
    agent any

    stages {
        stage('Checkout code') {
            steps {
                // Checkout code from GitHub and specify the branch
                git branch: 'main', url: 'https://github.com/Ayrola/JenkinsDemoRepo_7_30'
            }
        }

        stage('Set up .NET Core') {
            steps {
                bat '''
                echo Downloading .NET SDK 6.0 installer
                curl -L -o dotnet-sdk-6.0.100-win-x64.exe https://download.visualstudio.microsoft.com/download/pr/27c0ab28-4b11-4f67-8c0e-65e0d2bde2aa/1a4c31f875a539e88b2875e3b3629f5d/dotnet-sdk-6.0.100-win-x64.exe
                echo Installing .NET SDK 6.0
                dotnet-sdk-6.0.100-win-x64.exe /quiet /norestart
                '''
            }
        }

        stage('Restore dependencies') {
            steps {
                // Restore dependencies using the solution file
                bat 'dotnet restore SeleniumIde.sln'
            }
        }

        stage('Build') {
            steps {
                // Build the project using the solution file
                bat 'dotnet build SeleniumIde.sln --configuration Release'
            }
        }

        stage('Run tests') {
            steps {
                // Run tests using the solution file
                bat 'dotnet test SeleniumIde.sln --logger "trx;LogFileName=TestResults.trx"'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/TestResults/*.trx', allowEmptyArchive: true
            junit '**/TestResults/*.trx'
        }
    }
}