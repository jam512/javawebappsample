import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=<117a4d10-5a5a-41d0-a9f6-8f7c15c4ef4d>',
        'AZURE_TENANT_ID=<0adb040b-ca22-4ca6-9447-ab7b049a22ff>']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = '<QuickstartJenkins-rg
>'
      def webAppName = '<newjenkinss-app-yash5>'
      // login Azure
      withCredentials([usernamePassword(credentialsId: '<AzureServicePrinciple>', passwordVariable: 'y7q8Uvj1Ki6ZWCzXed5E-PNV.TpBxzOsSn', usernameVariable: '137be1ee-5a52-4de8-b1ac-8b97506406f1')]) {
       sh '''
          az login --service-principal -u y7q8Uvj1Ki6ZWCzXed5E-PNV.TpBxzOsSn  -p 137be1ee-5a52-4de8-b1ac-8b97506406f1 -t 0adb040b-ca22-4ca6-9447-ab7b049a22ff
          az account set -s $AZURE_SUBSCRIPTION_ID
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
