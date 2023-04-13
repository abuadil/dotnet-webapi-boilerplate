pipeline {
  agent any
  
  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/fullstackhero/dotnet-webapi-boilerplate.git'
      }
    }
    
    stage('Restore') {
      steps {
        sh 'dotnet restore'
      }
    }
    
    stage('Build') {
      steps {
        sh 'dotnet build'
      }
    }
    
    stage('Test') {
      steps {
        sh 'dotnet test'
      }
    }
    
    stage('Publish') {
      steps {
        sh 'dotnet publish -o publish'
      }
    }
    
    stage('Deploy') {
      steps {
        sh 'scp -r ./publish user@remote:/var/www/myapp'
      }
    }
  }
}
