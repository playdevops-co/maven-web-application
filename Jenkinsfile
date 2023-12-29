node{


def mavenHome = tool name: "maven3.9.6"

echo "Job Name is: ${env.JOB_NAME}" 
echo "Node Nsme is: ${env.NODE_NAME}"
echo "Build number is: ${env.BUILD_NUMBER}"
echo "Work space Name is: ${env.WORKSPACE}"
echo "Jenkins Home is: ${env.JENKINS_HOME}"
echo "Jenkins URL is: ${env.JENKINS_URL}"

properties([[$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

try{
// Get the code from github
stage('ChecOutCode'){
sendSlackNotifications("STARTED")
git branch: 'development', credentialsId: '039d9e6d-9b4b-4ab5-87da-663e8850d508', url: 'https://github.com/playdevops-co/maven-web-application.git'
}

// Build the Java code using maven
stage('Build'){
sh "${mavenHome}/bin/mvn clean package"
}

//Unit Testing using SonarQube
stage('SonarTest'){
sh "${mavenHome}/bin/mvn sonar:sonar"
}

//Upload Artifacts into Nexus repository
stage('UploadArtifactsIntoNexus'){
sh "${mavenHome}/bin/mvn deploy"
}

//deploy application into Tomcat server
stage('DeployIntoTomcatServer'){
sshagent(['abb1bf1a-35b9-4e66-becf-88e037dd888d']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war  ec2-user@172.31.3.186:/opt/tomcat9/webapps/"
}
}
}
catch(e){
currentBuild.result = "FAILURE"
throw e 
}
finally{
sendSlackNotifications(currentBuild.result)
}
}//closing node

def sendSlackNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    colorName = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    colorName = 'GREEN'
    colorCode = '#00FF00'
  } else {
    colorName = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
