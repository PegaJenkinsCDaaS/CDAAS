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
    stage('Validation') {
      parallel {
        stage('Run unit test') {
          steps {
            build 'Run Unit Tests'
          }
        }
        stage('Check compliance') {
          steps {
            echo 'Step to Check compliance score'
          }
        }
        stage('Regression tests') {
          steps {
            echo 'Step to execute "Run Selenium tests"'
          }
        }
      }
    }
    stage('Export from DEV') {
      steps {
        echo 'Step for Exporting product file from DEV environment'
        build 'Pega_Export'
      }
    }
    stage('Publish to Artifactory') {
      steps {
        echo 'Step to "Publish to Artifactory"'
        build(propagate: true, job: 'Artifactory_Upload')
      }
    }
    stage('Fetch from Artifactory') {
      steps {
        echo 'Step for Fetching zip file from Artifactory'
      }
    }
    stage('Deploy to TEST') {
      steps {
        echo 'Step for Deploying to TEST environment'
      }
    }
    stage('Deploy to ACC') {
      steps {
        echo 'Step for Deploying to Acceptance environment'
      }
    }
    stage('Deploy to PROD') {
      steps {
        echo 'Step to Deploy zip file to Production environment'
      }
    }
  }
}