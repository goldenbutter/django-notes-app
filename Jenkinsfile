pipeline {
    agent { label 'vinod' }

    parameters {
        string(name: 'IMAGE_NAME', defaultValue: 'notes-app', description: 'Docker image name')
        string(name: 'DOCKERHUB_REPO', defaultValue: 'notes-app', description: 'Docker Hub repository')
        string(name: 'TAG', defaultValue: 'latest', description: 'Image tag')
    }

    stages {
        stage('Git Clone') {
            steps {
                echo 'Cloning repository...'
                git url: 'https://github.com/goldenbutter/django-notes-app.git', branch: 'main'
            }
        }

        stage('Docker Image Build') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${params.IMAGE_NAME}:${params.TAG} ."
            }
        }

        stage('Push') {
            steps {
                echo 'Pushing image to Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHubCred',
                    usernameVariable: 'dockerHubUser',
                    passwordVariable: 'dockerHubPass'
                )]) {
                    sh """
                        echo "$dockerHubPass" | docker login -u "$dockerHubUser" --password-stdin
                        docker tag ${params.IMAGE_NAME}:${params.TAG} $dockerHubUser/${params.DOCKERHUB_REPO}:${params.TAG}
                        docker push $dockerHubUser/${params.DOCKERHUB_REPO}:${params.TAG}
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying with Docker Compose...'
                sh "docker compose up -d || docker-compose up -d"
            }
        }
    }
}
