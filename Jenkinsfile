pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Build'
        withAnt(installation: 'Ant Installation')
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