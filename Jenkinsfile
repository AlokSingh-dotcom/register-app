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
				git branch: 'main', url: 'https://github.com/Ashfaque-9x/register-app.git'
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
		 
	}
	
}
