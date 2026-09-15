pipeline{
	agent{label 'jenkins agent' }
	tools{
		jdk 'java17'
		maven 'Maven3'
	}
		
	environment {
		APP_NAME = "Mavenappplicatin"
		RELEASE = "1.0.0"
		DOCKER_USER = "alokdocio"
		DOCKER_PASS = "dckr_pat_Mekfs9k8_CKDnQ_pX_TlzvS1jmU"
		IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
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
            	sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar'
        }
    }
}
		stage("quality gate"){
			 steps{
				 script{
					 waitForQualityGate abortPipeline: false, credentialsId: 'jenkinssonar'
				 }
			 }
		}
		stage("Docker image build and push") {
   			steps {
        		script {
            		docker.withRegistry('https://index.docker.io/v1/', 'docker') {

                		docker_image = docker.build(IMAGE_NAME)

                		docker_image.push("${RELEASE}")
                		docker_image.push('latest')
            }
        }
    }
}
		 
	}
	
}
