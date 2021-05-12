import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
   // return [url: p.publishUrl, username: p.wecarebackofficeuser, password: p.L1tyL2xCHqJTyjgB3xDBaSA83NNA6uBTjhwRiYp9KD4lBJPgX2H4a0q7SAKk]
   return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=9daf9d86-59fc-4bc1-bbcd-dece2f0115df',
        'AZURE_TENANT_ID=b1f2944f-6fff-4fca-bb7a-a20bc23a73c0','AZURE_CLIENT_ID=9daf9d86-59fc-4bc1-bbcd-dece2f0115df']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'GodsEye'
      def webAppName = 'JenkinsTeste'
      // login Azure linha 27 az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID  
      withCredentials([usernamePassword(credentialsId:'c3185599-8bfc-4f16-a7b9-2b5e045da6ff', passwordVariable: 'DEVO@2021', usernameVariable: 'cmatos@descontel.pt')]) {
       sh '''
        
        
         az login -u cmatos@descontel.pt -p DEVO@2021.
          az account set -s $AZURE_SUBSCRIPTION_ID
        '''
      }
      // get publish settings linha 28 az login --service-principal -u 'cmatos@d-evo.com' -p 'DEVO@2021.' -t $AZURE_TENANT_ID 
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u devobackofficeuser -pDeVo@2021." 
      // :L1tyL2xCHqJTyjgB3xDBaSA83NNA6uBTjhwRiYp9KD4lBJPgX2H4a0q7SAKk"
      // linha 38 - u -> '$ftpProfile.username:$ftpProfile.password'
      // log out
      sh 'az logout'
    }
  }
}
