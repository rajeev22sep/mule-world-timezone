pipeline {
	agent any
	
	environment {
	
		ANYPOINT_CREDS = credentials('ANYPOINT_CREDENTIAL')
		
		// This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "desktop-66i8l1j:8096"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "nexus-releases"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "nexus"

	}
	

	stages {
	
		stage('Build') {
			steps {
				bat 'mvn -B -U -e -V clean -DskipTests package'	
			}
		
		}
		
		stage('Static Analysis') {  
			steps{
  					withSonarQubeEnv('SonarQubeServer') {
  						bat 'mvn verify sonar:sonar'
    		 			echo '********** Static Analysis Completed **********' 
    				}
  			}
		}
		
		

		stage('Munit Test') {
			steps {
				bat "mvn  -DskipTests test"
			}
		}
		
		stage("publish to Nexus") {
		
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    readXML = readMavenPom file:'';
                    echo " ********* Found POM.XML :  ${readXML} ********";
                    echo "**********The value of groupId is: ${readXML.groupId} **********";
                    echo "********** The value of artifactId is: ${readXML.artifactId} **********";
                    echo "********** The value of version is: ${readXML.version} **********";
                    echo "********** The value of packaging is: ${readXML.packaging} **********";
                    
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "\\target\\*.jar");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    //artifactPath = ".\\target\\mule-world-timezone-2.0.0-mule-application.jar";
                    echo "********** The value of artifactPath is: ${artifactPath} **********";
                     
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;
                    echo "********** The value of artifactExists is: ${artifactExists} **********";

                    if(artifactExists) {
                        echo "********** File: ${artifactPath}, group: ${readXML.groupId}, packaging: ${readXML.packaging}, version ${readXML.version} **********";

                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: readXML.groupId,
                            version: readXML.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: readXML.artifactId, classifier: '', file: artifactPath, type: readXML.packaging],

                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                               [artifactId: readXML.artifactId, classifier: '', file: 'pom.xml', type: 'pom']
                            ]
                        );
                        
                        echo "********** Artifacts and pom.xml both are uploaded **********";

                    } else {
                        error "********** File: ${artifactPath}, could not be found **********";
                    }
                }
            }
        }

		stage('Deploy DEV') {
		
		environment {
				CLIENT_ID = credentials('DEV_CLIENT_ID')
				CLIENT_SECRET = credentials('DEV_CLIENT_SECRET')
		}
	
			
			steps {
				bat 'mvn -U -V -e -B  -DskipTests deploy -Pch-dev -DmuleDeploy -Dcloudhub.userName="%ANYPOINT_CREDS_USR%" -Dcloudhub.password="%ANYPOINT_CREDS_PSW%" -Ddev.clientId="%CLIENT_ID%" -Ddev.clientSecret="%CLIENT_SECRET%" ' 
				
			}
		}
		
		
		stage('DEV Regression Testing') {
			steps {
				bat 'C:\\Users\\Rajeev\\AppData\\Roaming\\npm\\newman run D:\\InstalledSoftwares\\newman\\World-TimeZone.postman_collection.json --disable-unicode '
			}
		}
		
		
		stage('Deploy QA') {
		
		environment {
				CLIENT_ID = credentials('QA_CLIENT_ID')
				CLIENT_SECRET = credentials('QA_CLIENT_SECRET')
		}
	
			
			steps {
				bat 'mvn -U -V -e -B  -DskipTests deploy -Pch-qa -DmuleDeploy -Dcloudhub.userName="%ANYPOINT_CREDS_USR%" -Dcloudhub.password="%ANYPOINT_CREDS_PSW%" -Dqa.clientId="%CLIENT_ID%" -Dqa.clientSecret="%CLIENT_SECRET%" ' 
				
			}
		}
		
		stage('QA Regression Testing') {
			steps {
				bat 'C:\\Users\\Rajeev\\AppData\\Roaming\\npm\\newman run D:\\InstalledSoftwares\\newman\\World-TimeZone.postman_collection.json --disable-unicode '
			}
		}

		
	}
	
	
}
