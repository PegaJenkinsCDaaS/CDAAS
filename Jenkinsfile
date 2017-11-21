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
      parallel {
        stage('Run unit test') {
          steps {
            sh 'echo Gangadhar is working on it'
            build 'Pega_Validation'
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
        sh '''# Change the format of file to unix
set fileformat=unix
# Invoke PEGA utility to export product file
$PEGA_HOME/scripts/utils/prpcServiceUtils.sh export --connPropFile $PEGA_HOME/scripts/utils/${SystemName}_export.properties --artifactsDir $WORKSPACE
'''
      }
    }
    stage('Publish to Artifactory') {
      steps {
        echo 'Step to "Publish to Artifactory"'
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