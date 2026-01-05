@Library("Shared") _
pipeline {
    agent { label 'vinod' }

    parameters {
        string(name: 'IMAGE_NAME', defaultValue: 'notes-app', description: 'Docker image name')
        string(name: 'DOCKERHUB_REPO', defaultValue: 'notes-app', description: 'Docker Hub repository')
        string(name: 'TAG', defaultValue: 'latest', description: 'Image tag')
    }

    stages {
        stage('Clone') {
            steps {
                script {
                    echo 'Git repo Cloning.'
                    gitClone('https://github.com/goldenbutter/django-notes-app.git','main')
                    echo 'Git Cloning is successful.'
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker image.'
                sh "docker build -t ${params.IMAGE_NAME}:${params.TAG} ."
                    echo 'Docker Image Build is successful.'
            }
        }

        stage('Push') {
            steps {
                script {
                    echo 'Pushing Docker Image to Docker Hub.'
                    dockerPush(params.IMAGE_NAME, params.TAG, 'dockerHubCred', params.DOCKERHUB_REPO)
                    echo 'Docker Push is successful.'
                }
            }
        }

        stage('Deploy') {
          steps {
            echo 'Deploying with Docker Compose.'
            dir('django-notes-app') {
              sh 'docker compose down --remove-orphans || docker-compose down --remove-orphans'
              sh 'docker compose up -d --force-recreate --pull always || docker-compose up -d --force-recreate'
              echo 'Docker Compose Deployment is successful.'
            }
          }
        }
        
        stage('Library smoke') {
          steps {
            script {
              echo "Known globals: gitClone, dockerBuild, dockerPush should exist now."
            }
          }
        }
    }
}
