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

        sshagent(['ec2-ssh']) {

            bat '''
            ssh -o StrictHostKeyChecking=no ubuntu@EC2_PUBLIC_IP "
            docker pull yourdockerhubusername/flask-demo:latest &&
            docker stop flask-demo || true &&
            docker rm flask-demo || true &&
            docker run -d --name flask-demo -p 5000:5000 yourdockerhubusername/flask-demo:latest
            "
            '''
        }

    }

}

    }
}