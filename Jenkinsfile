pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *') 
    }

    options {
        skipDefaultCheckout(true)
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                script {
                    if (env.BRANCH_NAME != 'main') {
                        echo "Not on 'main' branch. Skipping build."
                        currentBuild.result = 'SUCCESS'
                        exit 0
                    }
                }
            }
        }

        stage('Restore dependencies') {
            steps {
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build --configuration Release --no-restore'
            }
        }

        stage('Run Unit Tests') {
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
