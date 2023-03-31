pipeline {
    agent { label 'windows' }
    
    environment {
        MSBUILD_PATH = "C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Enterprise\\MSBuild\\Current\\Bin"
    }
    
    stages {
        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }
        
        stage('Checkout Code') {
            steps {
                checkout([$class: 'GitSCM', 
                          branches: [[name: 'main']], 
                          doGenerateSubmoduleConfigurations: false, 
                          extensions: [], 
                          submoduleCfg: [], 
                          userRemoteConfigs: [[url: 'https://github.com/abuadil/dotnet-webapi-boilerplate.git']]])
            }
        }
        
        stage('Restore Packages') {
            steps {
                bat "\"C:\\Program Files\\dotnet\\dotnet.exe\" restore"
            }
        }
        
        stage('Increase Version') {
            steps {
                bat "dotnet tool install -g dotnet-version-cli"
                bat "dotnet-version newpatch"
            }
        }
        
        stage('Build') {
            steps {
                bat "\"$env:MSBUILD_PATH\\MSBuild.exe\" FSH.WebApi.sln /t:Build /p:Configuration=Release"
            }
        }
        
        stage('Unit Tests') {
            steps {
                bat "\"$env:MSBUILD_PATH\\MSBuild.exe\" FSH.WebApi.sln /t:VSTest /p:Configuration=Release /p:CollectCoverage=true /p:CoverletOutputFormat=opencover"
            }
        }
        
        stage('Convert Coverage Report') {
            steps {
                bat "dotnet tool install -g coverlet.console"
                bat "coverlet MyProject.Tests.dll --target dotnet --targetargs \"test MyProject.Tests.dll --no-build\" --format opencover"
            }
        }
        
        stage('Generate Coverage Report') {
            steps {
                bat "dotnet tool install -g reportgenerator"
                bat "reportgenerator -reports:coverage.opencover.xml -targetdir:coveragereport -reporttypes:Html"
            }
        }
        
        stage('Publish Coverage Report') {
            steps {
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'coveragereport', reportFiles: 'index.htm', reportName: 'Code Coverage Report'])
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/bin/Release/*.dll, **/bin/Release/*.pdb, **/coveragereport/**'
            }
        }
        
        stage('Deploy Artifacts') {
            steps {
                bat 'scp -r **/* user@remote:/path/to/deployment'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
