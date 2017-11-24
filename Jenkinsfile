pipeline {
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
		string(name: 'Application_List_for_Validation', defaultValue: 'HCLEnterprise', description: 'Application List For Compliance Score Check')
		string(name: 'Compliance_Threshold', defaultValue: '90', description: 'Compliance Threshold for an Application')
		
      }
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
        echo "Value of Application List: ${params.Application_List_for_Validation}"
		echo "Value of Application List: ${params.Compliance_Threshold}"
      }
    }
    stage('Validation') {
      parallel {
        stage('Run unit test') {
          steps {
            build(job: 'Run_Unit_Tests', parameters: [
                                                                                      string(name: 'DevelopmentURL',  value: params.DEV_Environment_URL),
                                                                                      string(name: 'AccessGroup',     value: params.AccessGroup_for_AUT)
                                                                                      ])
            }
          }
          stage('Check compliance') {
            steps {
              build(job: 'Check_Compliance_Score', parameters: [
                                                                           string(name: 'applicationList',  value: params.Application_List_for_Validation),
                                                                            string(name: 'pegaSourceURL',       value: params.DEV_Environment_URL),
                                                                            string(name: 'pegaSourceUser',      value: params.Username_for_Export),
                                                                            string(name: 'pegaSourcePassword',  value: params.Password_for_Export),
                                                                            string(name: 'emailRecipients',     value: params.EmailId_for_Notification),
																			string(name: 'ComplianceThreshold',  value: params.Compliance_Threshold)
                                                                            ])
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
          build(job: 'Pega_Export', parameters: [
                                                                            string(name: 'productName',         value: params.ProductName_for_Export),
                                                                            string(name: 'productVersion',      value: params.ProductVersion_for_Export),
                                                                            string(name: 'applicationName',     value: params.Application_name_for_Export),
                                                                            string(name: 'applicationVersion',  value: params.Application_version_for_Export),
                                                                            string(name: 'pegaSourceURL',       value: params.DEV_Environment_URL),
                                                                            string(name: 'pegaSourceUser',      value: params.Username_for_Export),
                                                                            string(name: 'pegaSourcePassword',  value: params.Password_for_Export),
                                                                            string(name: 'emailRecipients',     value: params.EmailId_for_Notification),
                                                                            ])
          }
        }
        stage('Publish to Artifactory') {
          steps {
            build(job: 'Artifactory_Upload', parameters: [
																																								                                                                             string(name: 'applicationName',     value: params.Application_name_for_Export),
																			string(name: 'applicationVersion',  value: params.Application_version_for_Export),
                                                                            string(name: 'emailRecipients',     value: params.EmailId_for_Notification),
                                                                            ])
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
