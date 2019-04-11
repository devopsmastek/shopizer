pipeline {

    agent any
 	parameters {
        string(defaultValue: "sachin.a.patil@oracle.com", description: 'What environment?', name: 'EmailRecipient')
        // choices are newline separated
        choice(choices: 'US-EAST-1\nUS-WEST-2', description: 'What AWS region?', name: 'region')
    }
    tools {
        maven 'jenkins_maven'
        jdk 'JAVA_1.8'
    }
    stages {
        stage ('Checkout'){
            steps {
            checkout scm
			
            }
   	    }
        stage('Build') {
            steps {
	       sh "echo ${params.EmailRecipient}"
	       sh "echo ${params.region}"    
               sh 'mvn clean package sonar:sonar -Dsonar.host.url=http://127.0.0.1:9000/ -DproxySet=true'
		   	nexusArtifactUploader(
    			nexusVersion: 'nexus3',
    			protocol: 'http',
    			nexusUrl: '127.0.0.1:9999',
    			groupId: 'com.mkyong',
    			version: '1.0-SNAPSHOT',
    			repository: 'maven-snapshots',
    			credentialsId: 'Mum_Nexus_server',
    			artifacts: [
        		[artifactId: 'CounterWebApp',
         		classifier: '',
         		file: 'target/CounterWebApp.war',
         		type: 'war']
    			]
 		)
           
   	    }
            
        }
		
		stage ('success'){
            steps {
                script {
                    currentBuild.result = 'SUCCESS'
                }
            }
        }
		
    }
	post {
        failure {
            script {
                currentBuild.result = 'FAILURE'
            }
        }

        always {
            step([$class: 'Mailer',
                notifyEveryUnstableBuild: true,
                recipients: "${params.EmailRecipient}",
                sendToIndividuals: true])
        }
    }
}
