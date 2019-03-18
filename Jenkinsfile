podTemplate(
  label: "maven-pod",
  cloud: "openshift",
  inheritFrom: "maven",
  containers: [
    containerTemplate(
      name: "jnlp",
      image: "docker-registry.default.svc:5000/jenkins/jenkins-agent-maven:latest",
    )
  ],
  volumes: [
    secretVolume(
      mountPath: "/home/jenkins/mvn-settings",
      secretName: "maven"
    )
  ]
) {
	node('maven-pod') {
	  // Define Maven Command to point to the correct
	  // settings for our Nexus installation
	  def mvnCmd = "mvn -Dsettings.security=/home/jenkins/mvn-settings/settings-security.xml -s /home/jenkins/mvn-settings/settings.xml"
	  def pom
	
	  // Checkout Source Code.
	  stage('Checkout Source') {
	    checkout scm
	  }
	  
	  stage('Get project version') {
	    pom = readMavenPom file: 'pom.xml'
	  }
		
	
	  // Using Maven build the jar files
	  // Do not run tests in this step
	  stage('Build pom') {
	    echo "Building version ${pom.version}"
	    sh "${mvnCmd} clean package -DskipTests"
	  }
	
	  // Publish the built war file to Nexus
	  stage('Publish to Nexus') {
	    echo "Publish to Nexus"
	    sh "${mvnCmd} deploy -DskipTests=true"
	  }
	}
}
