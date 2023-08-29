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


        

    }
}
