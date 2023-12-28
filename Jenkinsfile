node{

def mavenHome = tool name: 'maven3.9.6'

stage('checkoutCode'){
git credentialsId: '039d9e6d-9b4b-4ab5-87da-663e8850d508', url: 'https://github.com/playdevops-co/maven-web-application'
}

stage('build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage('Code Test'){
sh "${mavenHome}/bin/mvn sonar:sonar"
}

stage('UploadAppIntoNexusArtifactory'){
sh "${mavenHome}/bin/mvn deploy"
}

stage('DeployApplicationIntoServer'){
sshagent(['abb1bf1a-35b9-4e66-becf-88e037dd888d']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war  ec2-user@172.31.3.186:/opt/tomcat9/webapps/"
}
}
}
