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

		stage('Test') {
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
				bat 'mvn -U -V -e -B  -DskipTests deploy -Pch-dev -DmuleDeploy ' 
				
			}
		}
		
		stage('Deploy QA') {
		
		environment {
				CLIENT_ID = credentials('QA_CLIENT_ID')
				CLIENT_SECRET = credentials('QA_CLIENT_SECRET')
		}
	
			
			steps {
				bat 'mvn -U -V -e -B   deploy -Pch-qa -DmuleDeploy ' 
				
			}
		}

		
	}
}
