pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup .NET') {
            when { branch 'main' }
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

        stage('Restore dependencies') {
            when { branch 'main' }
            steps {
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            when { branch 'main' }
            steps {
                bat 'dotnet build --configuration Release --no-restore'
            }
        }

        stage('Run Unit Tests') {
            when { branch 'main' }
            steps {
                bat '''
                    dotnet test Horizons.Tests.Unit\\Horizons.Tests.Unit.csproj ^
                        --configuration Release ^
                        --no-build ^
                        --verbosity normal ^
                        --logger trx ^
                        --results-directory "TestResults\\Unit"
                '''
            }
        }

        stage('Run Integration Tests') {
            when { branch 'main' }
            steps {
                bat '''
                    dotnet test Horizons.Tests.Integration\\Horizons.Tests.Integration.csproj ^
                        --configuration Release ^
                        --no-build ^
                        --verbosity normal ^
                        --logger trx ^
                        --results-directory "TestResults\\Integration"
                '''
            }
        }
    }
}
