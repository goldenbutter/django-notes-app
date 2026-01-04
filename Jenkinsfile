pipeline {
    agent {label "vinod"}

    stages {
        stage('Code') {
            steps {
                echo 'Cloning code'
                git url: "https://github.com/goldenbutter/django-notes-app.git", branch:"main"
                echo "Code clone successful"
            }
        }
        stage('Build') {
            steps {
                echo 'Build code'
                sh "whoami"
                sh "docker build -t notes-app:latest ."
            }
        }
        stage('Push') {
            steps {
                echo 'Push code to docker hub'
                withCredentials([usernamePassword(
                    credentialsId:"dockerHubCred",
                    usernameVariable:"dockerHubUser", 
                    passwordVariable:"dockerHubPass")]){
                sh "echo $dockerHubPass | docker login -u ${env.dockerHubUser} --password-stdin"
                sh "docker image tag notes-app:latest ${env.dockerHubUser}/notes-app:latest"
                sh "docker push ${env.dockerHubUser}/notes-app:latest"
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy code'
                sh "docker-compose up -d"
            }
        }
    }
}