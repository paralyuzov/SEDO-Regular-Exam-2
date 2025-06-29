pipeline {
    agent {
        label 'any'
    }

    
    triggers {
        pollSCM('H/5 * * * *')
    }
    
    stages {
        stage('Checkout code') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup .NET') {
            steps {
                bat '''
                    dotnet --version
                    if %errorlevel% neq 0 (
                        echo .NET SDK 8.0.x not found. Please install .NET 8.0 SDK
                        exit /b 1
                    )
                    echo .NET 8.0 SDK is ready
                '''
            }
        }
        
        stage('Cache dependencies') {
            steps {
                echo 'Using local NuGet package cache (automatic in Jenkins)'
            }
        }
        
        stage('Restore dependencies') {
            steps {
                bat 'dotnet restore'
            }
        }
        
        stage('Build solution') {
            steps {
                bat 'dotnet build --configuration Release --no-restore'
            }
        }
        
        stage('Run unit tests') {
            steps {
                bat '''
                    dotnet test Horizons.Tests.Unit\\Horizons.Tests.Unit.csproj ^
                        --configuration Release ^
                        --no-build ^
                        --verbosity normal ^
                        --logger trx ^
                        --results-directory "TestResults"
                '''
            }
        }
        
        stage('Run integration tests') {
            steps {
                bat '''
                    dotnet test Horizons.Tests.Integration\\Horizons.Tests.Integration.csproj ^
                        --configuration Release ^
                        --no-build ^
                        --verbosity normal ^
                        --logger trx ^
                        --results-directory "TestResults"
                '''
            }
        }
    }
}
