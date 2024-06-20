pipeline {
    agent any 

    stages {
        stage("Clone Code") {
            steps {
                echo "Cloning the code"
                git url: "https://github.com/atulchambial/django-notes-app.git", branch: "main"
            }
        }

        stage("Build") {
            steps {
                echo "Building the image"
                sh "docker build -t my-note-app ."
            }
        }

        stage("Push to Docker Hub") {
            steps {
                echo "Pushing the image to Docker Hub"
                withCredentials([usernamePassword(credentialsId: "dockerHub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
                    script {
                        def imageName = "${env.dockerHubUser}/my-note-app:latest"
                        sh "docker tag my-note-app ${imageName}"
                        sh "echo ${dockerHubPass} | docker login -u ${dockerHubUser} --password-stdin"
                        sh "docker push ${imageName}"
                    }
                }
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying the container"
                script {
                    try {
                        sh "docker-compose down"
                    } catch (Exception e) {
                        echo "No containers to bring down"
                    }
                    sh "docker-compose up -d"
                }
            }
        }
    }
}

