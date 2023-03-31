pipeline {
    agent {
        node {
            label 'windows'
            // Set the path to the .NET SDK on the agent
            // You may need to adjust this based on your environment
            tool 'dotnet-sdk-6.0.100-preview.7.21379.14-x64'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the Git repository
                git 'https://github.com/abuadil/dotnet-webapi-boilerplate.git'
            }
        }
        stage('Restore packages') {
            steps {
                // Restore NuGet packages
                bat 'dotnet restore'
            }
        }
        stage('Clean') {
            steps {
                // Clean the workspace
                bat 'dotnet clean'
            }
        }
        stage('Increase version') {
            steps {
                // Increase the version number in the AssemblyInfo.cs file
                bat 'dotnet build /version-suffix $BUILD_NUMBER'
            }
        }
        stage('Build') {
            steps {
                // Build the project
                bat 'dotnet build --configuration Release'
            }
        }
        stage('Run unit tests') {
            steps {
                // Run unit tests and generate a coverage report in Cobertura format
                bat 'dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=cobertura'
            }
            post {
                always {
                    // Convert the Cobertura report to XML format
                    bat 'dotnet tool install -g dotnet-reportgenerator-globaltool'
                    bat 'reportgenerator "-reports:**/coverage.cobertura.xml" "-targetdir:coverage"'
                }
            }
        }
        stage('Publish HTML report') {
            steps {
                // Publish the HTML coverage report as a build artifact
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'coverage', reportFiles: 'index.htm', reportName: 'Code Coverage Report'])
            }
        }
        stage('Archive artifacts') {
            steps {
                // Archive the built application as a build artifact
                archiveArtifacts '**/bin/Release/**'
            }
        }
        stage('Deploy artifacts') {
            steps {
                // Deploy the built application to a remote server using SCP
                sshPublisher(publishers: [sshPublisherDesc(configName: 'myserver', transfers: [sshTransfer(execCommand: 'sudo systemctl stop myservice', execTimeout: 120000), sshTransfer(execCommand: 'mkdir -p /path/to/deploy', execTimeout: 120000), sshTransfer(execCommand: 'scp -r ./bin/Release user@myserver:/path/to/deploy', execTimeout: 120000), sshTransfer(execCommand: 'sudo systemctl start myservice', execTimeout: 120000)], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
            }
        }
    }
}
