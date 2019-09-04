pipeline {
    agent {
        docker {
            image 'ortussolutions/commandbox'
        }
    }
    stages {
        stage('Static Analysis') { 
            steps {
                sh 'box install commandbox-cflint'
                sh 'box cflint reportLevel=ERROR'
            }
        }
    }
}
