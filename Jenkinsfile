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
