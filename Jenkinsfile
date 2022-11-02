node{

def mavenHome = tool name : "maven3.8.5"

echo "Build number: ${env.BUILD_NUMBER}"
echo "Job Name: ${env.JOB_NAME}"
echo "Node Name: ${env.NODE_NAME}"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])

try{
sendslacknotifications("STARTED")
stage('checkoutcode'){
git branch: 'development', credentialsId: '6b5476ae-8163-4b3b-94c2-e495dc8e6184', url: 'https://github.com/karthiksoftsolution/maven-web-application.git'
}

stage('build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage('sonarqubereport'){
sh "${mavenHome}/bin/mvn sonar:sonar"
}
  
stage('uploadartifacts'){
sh "${mavenHome}/bin/mvn deploy"
}

stage('Deploy application Tomcat server'){
sshagent(['d71e3521-85ee-44a3-9446-1617e770bfa3']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@3.110.216.159:/opt/apache-tomcat-9.0.68/webapps/"
}
}

}//try closing
catch(e){
currentBuild.result = "FAILURE"
}//catch block closing
finally{
sendslacknotifications(currentBuild.result)
}//finally closing

}//nodeclosing


def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
