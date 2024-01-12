pipeline {
	agent any
	
	environment {
		ANYPOINT_CREDS = credentials('ANYPOINT_CREDENTIAL')
	}
	

	stages {
		stage('Build') {
			steps {
				bat 'mvn -B -U -e -V clean -DskipTests package'
			}
		}

		stage('Munit Test') {
			steps {
				bat "mvn  test"
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
