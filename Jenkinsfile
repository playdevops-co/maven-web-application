node{

def mavenHome = tool name: 'maven3.9.6'

echo "The Node name is: ${env.NODE_NAME}"
echo "The Job Name is: ${env.JOB_NAME}"
echo "The Build Name is : ${env.BUILD_DISPLAY_NAME}"


stage('CheckOutCode'){
git branch: 'development', credentialsId: '039d9e6d-9b4b-4ab5-87da-663e8850d508', url: 'https://github.com/playdevops-co/maven-web-application.git'
}

stage('Build'){
sh "$mavenHome/bin/mvn clean package"
}

stage('SonarQubeProject'){
sh "$mavenHome/bin/mvn sonar:sonar"
}

stage('UploadArtifactIntoNexus'){
sh "$mavenHome/bin/mvn deploy"
}
stage('DeployIntoTomcat'){
sshagent(['abb1bf1a-35b9-4e66-becf-88e037dd888d']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war  ec2-user@172.31.3.186:/opt/tomcat9/webapps/"
}
 
}

}

// Single line comment

/*  

Multiple line comment
*/

// Using "def" we can decalre the variable in groovy script
