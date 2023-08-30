pipeline{
    agent{
        label "jenkins-agent"
    }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    } 
    environment{
        APP_NAME="java-project"
        RELEASE="1.0.0"
        DOCKER_USER= "alaaeddinezarrouk"
        DOCKER_PASS= "dockerhub"
        IMAGE_NAME="${DOCKER_USER}"+"/"+"${APP_NAME}"
        IMAGE_TAG="${RELEASE}-${BUILD_NUMBER}"
        AZURE_SP_USERNAME="671b61dd-a40a-4046-bbbb-f7f23718e058"
        AZURE_SP_PASSWORD="TpO8Q~RrmlGI-bp_zOKlMqNDcxD5d22ajJ.Amax0"
        AZURE_SP_TENANT="765eb9b2-efb3-4eac-a1af-4ad1ffe1a8eb"
    }
    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }
        }

        stage("Check out SCM"){
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/alaaeddinezarrouk/java-project.git'
            }
        }
        stage("Build Application"){
            steps {
                sh "mvn clean package" 
            }
        }

        stage("Test application"){
            steps {
                sh "mvn test"
            }
        }
        stage("Soanrqube analysis"){
            steps {
                script{
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token'){
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage("Quality gate"){
            steps {
                script{
                    waitForQualityGate abortPipeline: false,credentialsId: 'jenkins-sonarqube-token'
                }
            }

        }
        stage("Build & push docker image") {
            steps {
                script {
                    docker.withRegistry("", DOCKER_PASS) {
                        def customImage = docker.build("${IMAGE_NAME}")
                        customImage.push("${IMAGE_TAG}")
                        customImage.push("latest")
                    }
                }
            }
        }
        stage("Deploy to Azure Web App") {
            steps {
                script {
                    def azureWebAppName = "prod-app-cloudtemple"
                    def azureResourceGroup = "demo"
                    def azureDockerImage = "${DOCKER_USER}/${APP_NAME}:${RELEASE}-${BUILD_NUMBER}"

                    // Log in to Azure (assuming you have service principal credentials)
                    sh "az login --service-principal -u $AZURE_SP_USERNAME -p $AZURE_SP_PASSWORD --tenant $AZURE_SP_TENANT"

                    // Set the Docker image for Azure Web App
                    sh "az webapp config container set --name ${azureWebAppName} --resource-group ${azureResourceGroup} --docker-custom-image-name ${azureDockerImage}"

                    // Restart Azure Web App to apply changes
                    sh "az webapp restart --name ${azureWebAppName} --resource-group ${azureResourceGroup}"
                }
            }
        }


        

    }
}
