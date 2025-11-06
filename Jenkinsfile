pipeline {
    agent any
    environment {
        IMAGE_NAME = "webapp"
        CONTAINER_NAME = "webapp-container"
        PORT = "8081"
    }
    stages {
        stage('Clone') {
            steps {
                slackSend(channel: '#tous-devops-jenkins', color: '#FFFF00', message: "*Pipeline démarré* sur `dev`\n:git: *Clone* en cours...")
                checkout scm
                slackSend(channel: '#tous-devops-jenkins', color: '#36A64F', message: ":white_check_mark: *Clone* OK")
            }
        }
        stage('Build') {
            steps {
                slackSend(channel: '#tous-devops-jenkins', color: '#FFFF00', message: ":hammer: *Build* en cours...")
                sh "docker build -t ${IMAGE_NAME}:latest ."
                slackSend(channel: '#tous-devops-jenkins', color: '#36A64F', message: ":package: *Build* OK")
            }
        }
        stage('Deploy') {
            steps {
                slackSend(channel: '#tous-devops-jenkins', color: '#FFFF00', message: ":rocket: *Deploy* en cours...")
                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE_NAME}:latest
                '''
                script {
                    slackSend(channel: '#tous-devops-jenkins', color: '#36A64F', message: ":tada: *Deploy* OK → http://localhost:${PORT}")
                }
            }
        }
    }
    post {
        always { slackSend(channel: '#tous-devops-jenkins', color: currentBuild.currentResult == 'SUCCESS' ? '#36A64F' : '#FF0000', message: "*Pipeline terminé* : ${currentBuild.currentResult}") }
        failure { slackSend(channel: '#tous-devops-jenkins', color: '#FF0000', message: ":x: ÉCHEC - Voir Jenkins") }
    }
}
