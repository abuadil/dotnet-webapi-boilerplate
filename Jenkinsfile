pipeline {
  agent any
  stages {
    stage('restore') {
      steps {
        bat(script: 'dotnet restore', returnStatus: true)
      }
    }

    stage('') {
      steps {
        bat(script: 'dotnet build', returnStatus: true)
      }
    }

    stage('Test') {
      steps {
        bat 'dotnet test'
      }
    }

  }
}