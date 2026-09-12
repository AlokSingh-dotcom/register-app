pipeline{
	agent{label 'jenkins agent' }
	tools{
		jdk 'java17'
		maven 'Maven3'
		
	}
	stages{
		stage("cleanup workspace"){
			steps{
			cleanWs()
			}
		}
		stage("Checkout from scm"){
			steps{
				git branch: 'main', url: 'https://github.com/AlokSingh-dotcom/register-app.git'
			}
		}
		
		stage("build application"){
			steps{
				sh "mvn clean package"
			}
		}
		stage("test ur application"){
			steps{
				 sh "mvn test"
			}
		}
		stage("sonarqube analysis") {
    		steps {
        		withSonarQubeEnv(
            		installationName: 'sonarqube_server',
            		credentialsId: 'jenkinssonar'
       		 ) {
            	sh 'mvn sonar:sonar'
        }
    }
}
		 
	}
	
}
