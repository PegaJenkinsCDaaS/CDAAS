pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        sh '''echo PEGA_HOME = $PEGA_HOME
echo JENKINS_HOME = $JENKINS_HOME
echo WORKSPACE = $WORKSPACE'''
      }
    }
    stage('Run unit test') {
      steps {
        sh 'echo Gangadhar is working on it'
      }
    }
  }
}