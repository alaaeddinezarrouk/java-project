pipeline{
    agent{
        label "jenkins-agent"
    }
    tools {
        jdk 'Java17'
        maven 'Maven3'
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
                        sh "mvn test"
                    }
                }
                
                
            }

        }

        

    }
}
