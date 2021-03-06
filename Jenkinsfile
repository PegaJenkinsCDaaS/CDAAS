pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        echo 'Step to perform initialisation'
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
        mail(subject: "Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) started", body: """<p>STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p><p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""", from: 'pegacdaas@jenkins.com', replyTo: params.EmailId_for_Notification, to: params.EmailId_for_Notification)
        //slackSend(channel: 'pegacdaas', message: "Job: '${env.JOB_NAME}' with build number: '${env.BUILD_NUMBER}' started")
      }
    }    
    stage('Checkout') {
      steps ('Checkout Build script') {
        checkout scm
      }
    }
    stage('Validation') {      
        steps ('Unit test') {
            echo 'Step to execute Selenium tests'
        build(job: 'Pega_Test', parameters: [
                                              string(name: 'sourceURL',         value: params.Dev_Test_URL),
                                              ])
          }
    }
    stage('Export from DEV') {
      steps {
        echo 'Step to export deployment archive from DEV environment'
        build(job: 'Pega_Export', parameters: [
                                                  string(name: 'productName',         value: params.ProductName_for_Export),
                                                  string(name: 'productVersion',      value: params.ProductVersion_for_Export),
                                                  string(name: 'applicationName',     value: params.Application_name_for_Export),
                                                  string(name: 'applicationVersion',  value: params.Application_version_for_Export),
                                                  string(name: 'SourceHost',       value: params.DEV_Environment_URL),
                                                  string(name: 'SourceUser',      value: params.Username_for_Export),
                                                  string(name: 'SourcePassword',  value: params.Password_for_Export),
                                                  ])
            }
      }
      stage('Publish to Artifactory') {
        steps {
          echo 'Step to upload deployment archive to Artifactory'
           build(job: 'Artifactory_Upload')  
        }
       }
        stage('Fetch from Artifactory') {
          steps {
            echo 'Step to fetch deployment archive from Artifactory'
            build(job: 'Artifactory_Download')
            }
          }
          stage('Deploy to TEST') {
            steps {
              echo 'Step to perform deployment to TEST environment'
	        build(job: 'Pega_Import', parameters: [
	                                              string(name: 'productName',         value: params.ProductName_for_Export),
	                                              string(name: 'productVersion',      value: params.ProductVersion_for_Export),
	                                              string(name: 'applicationName',     value: params.Application_name_for_Export),
	                                              string(name: 'applicationVersion',  value: params.Application_version_for_Export),
	                                              string(name: 'TargetHost',       value: params.TST_Environment_URL),
	                                              string(name: 'TargetUser',      value: params.Username_for_Import),
	                                              string(name: 'TargetPassword',  value: params.Password_for_Import),
	                                              ])
                                                  
               }
            }
            
	    stage('Regression Test') { 
          steps {
            echo 'Step to execute Selenium tests'              
	        build(job: 'Pega_Test', parameters: [
                                                  string(name: 'sourceURL',         value: params.sourceURL),
                                                  ])
              }
	    }
        stage('Finalize') {
          steps {
            echo 'Step to notify and perform cleanup tasks'
            mail(subject: "Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) completed", body: """<p>COMPLETED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p><p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""", from: 'pegacdaas@jenkins.com', replyTo: params.EmailId_for_Notification, to: params.EmailId_for_Notification)
            //slackSend(channel: 'pegacdaas', message: "Job: '${env.JOB_NAME}' with build number: '${env.BUILD_NUMBER}' completed")
          }
        }
      }
	  parameters {
	  	string(name: 'AccessGroup_for_AUT', defaultValue: 'HRServices:Administrators', description: 'Access group used for Automated unit')
	    string(name: 'ProductName_for_Export', defaultValue: 'HRServices', description: 'Name of the Rule-Admin-Product rule to be used for Export from Development environment')
	    string(name: 'ProductVersion_for_Export', defaultValue: '01.01.02', description: 'Version number of the Product rule')
	    string(name: 'Application_name_for_Export', defaultValue: 'HRServices', description: 'Name of application for Export')
	    string(name: 'Application_version_for_Export', defaultValue: '01.01.01', description: 'Version number of application for Export')
	    string(name: 'Username_for_Export', defaultValue: 'hbagalk', description: 'Pega operator id used for Export')
	    string(name: 'Password_for_Export', defaultValue: 'rules', description: 'Pega operator password used for Export')
	    string(name: 'Username_for_Import', defaultValue: 'administrator@pega.com', description: 'Pega operator id used for Import')
	    string(name: 'Password_for_Import', defaultValue: 'install', description: 'Pega operator password used for Import')
	    string(name: 'EmailId_for_Notification', defaultValue: 'titto.t@hcl.com', description: 'Email address used to notify status of deployment')
	    string(name: 'Application_List_for_Validation', defaultValue: 'HCLEnterprise', description: 'Application List For Compliance Score Check')
	    string(name: 'Compliance_Threshold', defaultValue: '90', description: 'Compliance Threshold for an Application')
	    string(name: 'productName',         defaultValue: 'params.ProductName_for_Export')
		string(name: 'productVersion',      defaultValue: 'params.ProductVersion_for_Export')
		string(name: 'applicationName',     defaultValue: 'params.Application_name_for_Export')
		string(name: 'applicationVersion',  defaultValue: 'params.Application_version_for_Export')
		string(name: 'pegaSourceURL',       defaultValue: 'params.DEV_Environment_URL')
		string(name: 'pegaSourceUser',      defaultValue: 'params.Username_for_Export')
		string(name: 'pegaSourcePassword',  defaultValue: 'params.Password_for_Export')
		string(name: 'emailRecipients',     defaultValue: 'params.EmailId_for_Notification')
	  }
	}