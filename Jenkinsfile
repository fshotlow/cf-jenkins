pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Build'
        withAnt(installation: '/opt/ant')
      }
    }
    stage('Publish') {
      steps {
        echo 'Publish'
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploy'
      }
    }
  }
}