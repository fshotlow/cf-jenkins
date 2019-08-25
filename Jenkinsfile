pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        withAnt(installation: '/opt/ant')
      }
    }
  }
  environment {
    Build = ''
  }
}