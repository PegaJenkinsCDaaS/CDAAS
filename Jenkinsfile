pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        echo "Value of DEV_Environment_URL: ${params.DEV_Environment_URL}"
        echo "Value of AccessGroup_for_AUT: ${params.AccessGroup_for_AUT}"
        echo "Value of ProductName_for_Export: ${params.ProductName_for_Export}"
        echo "Value of ProductVersion_for_Export: ${params.ProductVersion_for_Export}"
        echo "Value of Application_name_for_Export: ${params.Application_name_for_Export}"
        echo "Value of Application_version_for_Export: ${params.Application_version_for_Export}"
        echo "Value of Username_for_Export: ${params.Username_for_Export}"
        echo "Value of Password_for_Export: ${params.Password_for_Export}"
        echo "Value of EmailId_for_Notification: ${params.EmailId_for_Notification}"
        
        build job: 'HelloWorld', 
          parameters: [
            string(name: 'HelloWorldParam', value: ${params.DEV_Environment_URL})
          ]

      }
    }
    stage('Validation') {
      parallel {
        stage('Run unit test') {
          steps {
            build 'Run_Unit_Tests'
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
        build(job: 'Pega_Export', propagate: true)
      }
    }
    stage('Publish to Artifactory') {
      steps {
        echo 'Step to "Publish to Artifactory"'
        copyArtifacts(projectName: 'Pega_Export', parameters: 'EXPORT_BUILD_NUMBER')
        build 'Artifactory_Upload'
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
  environment {
    EXPORT_BUILD_NUMBER = '$BUILD_NUMBER'
  }
  parameters {
    string(name: 'DEV_Environment_URL', defaultValue: 'http://34.235.52.21:8780', description: 'URL containing protocol, hostname and port number for Development environment')
    string(name: 'AccessGroup_for_AUT', defaultValue: 'HRServices:Administrators', description: 'Access group used for Automated unit')
    string(name: 'ProductName_for_Export', defaultValue: 'HRServices', description: 'Name of the Rule-Admin-Product rule to be used for Export from Development environment')
    string(name: 'ProductVersion_for_Export', defaultValue: '01.01.02', description: 'Version number of the Product rule')
    string(name: 'Application_name_for_Export', defaultValue: 'HRServices', description: 'Name of application for Export')
    string(name: 'Application_version_for_Export', defaultValue: '01.01.01', description: 'Version number of application for Export')
    string(name: 'Username_for_Export', defaultValue: 'ExportImport@HRServices', description: 'Pega operator id used for Export')
    string(name: 'Password_for_Export', defaultValue: 'PegaCDaaS', description: 'Pega operator password used for Export')
    string(name: 'EmailId_for_Notification', defaultValue: 'titto.t@hcl.com', description: 'Email address used to notify status of deployment')
  }
}
