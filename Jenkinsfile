pipeline {
   agent any
   environment {
    runVeracode = true
   }
   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "M3"
   }
   //parameters {
    //  // Toggle this to turn on/off static application scanning.
    //  // Enabling this will employ Veracode code security scanning
    // booleanParam(name: 'ENABLE_SAST', defaultValue: true, description: 'Set to true to enable SAST scanning')
   //}
   stages {
      stage('Build') {
         steps {
            // Get some code from a GitHub repository
            git 'https://github.com/yokasou/helloworld.git'

            // Run Maven on a Unix agent.
            //sh "mvn -Dmaven.test.failure.ignore=true clean package"

            // To run Maven on a Windows agent, use
            //bat "mvn -Dmaven.test.failure.ignore=true clean package"
            bat "mvn clean package -DskipTests"
         }
         post {
            // If Maven was able to run the tests, even if some of the test
            // failed, record the test results and archive the jar file.
            success {
               //junit '**/target/surefire-reports/TEST-*.xml'
               archiveArtifacts 'target/*.jar'
            }
         }
      }

      stage('Veracode') {
        when {
          //expression { return params.ENABLE_SAST }
          expression { return env.runVeracode == 'true'}
        }
		steps{
            withCredentials([usernamePassword(credentialsId: 'veracode-credentials', passwordVariable: 'veracode_password', usernameVariable: 'veracode_username')]){
				veracode applicationName: 'MCap', canFailJob: false, debug: true, createSandbox: false, sandboxName: 'test-sandbox', scanName: "${env.JOB_NAME}-${env.BUILD_NUMBER}", timeout: 60, uploadExcludesPattern: '**/*@tmp/**,**/springboot-*.jar,**/executable-*.jar', uploadIncludesPattern: '**/**.jar', vid: veracode_username, vkey: veracode_password, waitForScan: false
            }
        }
	}
   }
}