pipeline {

    agent {
        label 'jenkins agent'
    }

    tools {
        jdk 'java17'
        maven 'Maven3'
    } 

    environment {
        APP_NAME    = "mavenappplicatin"
        RELEASE     = "1.0.0"
        DOCKER_USER = "alokdocio"
        IMAGE_NAME  = "${DOCKER_USER}/${APP_NAME}"
    }

    stages {

        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main',
                    url: 'https://github.com/AlokSingh-dotcom/register-app.git'
            }
        }

        stage("Build & Test Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv(
                    installationName: 'sonarqube_server',
                    credentialsId: 'jenkinssonar'
                ) {
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate(
                        abortPipeline: true,
                        credentialsId: 'jenkinssonar'
                    )
                }
            }
        }

        stage("Docker Image Build") {
            steps {
                script {
                    docker_image = docker.build(
                        "${IMAGE_NAME}:${RELEASE}"
                    )
                }
            }
        }

        stage("Trivy Security Scan") {
            steps {
                sh """
                    docker run --rm \
                    -v /var/run/docker.sock:/var/run/docker.sock \
                    aquasec/trivy image \
                    --exit-code 1 \
                    --severity HIGH,CRITICAL \
                    ${IMAGE_NAME}:${RELEASE}
                """
            }
        }

        stage("Docker Image Push") {
            steps {
                script {
                    docker.withRegistry(
                        'https://index.docker.io/v1/',
                        'docker'
                    ) {
                        docker_image.push("${RELEASE}")
                        docker_image.push("latest")
                    }
                }
            }
        }

        stage("Cleanup Docker Images") {
            steps {
                sh "docker rmi ${IMAGE_NAME}:${RELEASE} || true"
                sh "docker rmi ${IMAGE_NAME}:latest || true"
            }
        }
    }
}
