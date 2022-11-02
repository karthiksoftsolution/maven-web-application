node{

def mavenHome = tool name : "maven3.8.5"

echo "Build number: ${env.BUILD_NUMBER}"
echo "Job Name: ${env.JOB_NAME}"
echo "Node Name: ${env.NODE_NAME}"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])

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

}
