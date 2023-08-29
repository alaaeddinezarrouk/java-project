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

    }
}
