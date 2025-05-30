pipeline {

    agent {
    docker {
        image 'node:18' // has npm and node
        args '-v /var/run/docker.sock:/var/run/docker.sock'
    }
}

    tools {
        jdk 'jdk17'
        nodejs 'node23'
    }

    stages {
        stage ("clean workspace") {
            steps {
                cleanWs()
            }
        }

        stage ("Git Checkout") {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],  // use 'main' or your repo branch
                    userRemoteConfigs: [[
                        url: 'https://github.com/veerdevops-git/project-zomat.git'
                        // , credentialsId: 'your-credentials-id' // if private repo
                    ]]
                ])
            }
        }

        stage ("Build Docker Image") {
            steps {
                sh "docker build -t zomato ."
            }
        }

        stage ("Tag & Push to DockerHub") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        sh "docker tag zomato veerannadoc/zomato:latest"
                        sh "docker push veerannadoc/zomato:latest"
                    }
                }
            }
        }

        stage ("Deploy to Container") {
            steps {
                sh 'docker run -d --name zomato -p 3000:3000 veerannadoc/zomato:latest'
            }
        }
    }
}
