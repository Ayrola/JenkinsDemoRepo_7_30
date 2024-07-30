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
                curl -L -o dotnet-sdk-6.0.132-win-x86.exe https://download.visualstudio.microsoft.com/download/pr/ad59f1d1-5f19-4474-86be-2f09ab195618/5c7a64445dae84e386bb88e1f6ac09e4/dotnet-sdk-6.0.132-win-x86.exe
                echo Installing .NET SDK 6.0
                dotnet-sdk-6.0.132-win-x86.exe /quiet /norestart
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
		
		stage('Convert TRX to JUnit XML') {
            steps {
                bat '''
                echo Converting TRX to JUnit XML
                curl -L -o trx2junit.zip https://github.com/gfoidl/trx2junit/releases/download/v1.5.0/trx2junit.zip
                powershell -command "Expand-Archive -Path trx2junit.zip -DestinationPath . -Force"
                .\\trx2junit\\trx2junit.exe .\\SeleniumIDE\\TestResults\\TestResults.trx
                '''
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