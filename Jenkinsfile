pipeline {

    agent any

    environment {
        IMAGE_NAME = "ephraimimmanuel/week2-demo"
    }

    stages {

        stage('Clone') {
    steps {
        git branch: 'main',
            url: 'https://github.com/Ephraimimmanuel/week2-miniprj.git'
    }
}

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    bat '''
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    docker tag %IMAGE_NAME% %IMAGE_NAME%:latest
                    docker push %IMAGE_NAME%:latest
                    '''
                }
            }
        }
        stage('Deploy to EC2') {
    steps {
        bat '''
        ssh -o StrictHostKeyChecking=no -i "C:/ProgramData/Jenkins/.ssh/week2-miniprj.pem" ubuntu@13.203.79.0 "
            docker pull ephraimimmanuel/week2-demo:latest &&
            docker stop week2-demo || true &&
            docker rm week2-demo || true &&
            docker run -d --name week2-demo -p 5000:5000 ephraimimmanuel/week2-demo:latest
        "
        '''
    }
}

    }
}