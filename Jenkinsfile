pipeline {
     agent any
    
    environment {
        APP_NAME="python-flask-app"
        Docker_User_Name="harisantosh"
        IMAGE_NAME="${Docker_User_Name}"+"/"+"${APP_NAME}"
    }
    stages{
        stage('checkout'){
            steps{
                git credentialsId: 'GITHUB-CREDS', url: 'https://github.com/santosh189/flask-portfolio.git'
            }
        }
        stage('install dependencies') {
            steps{
                sh 'pip install -r requirements.txt'
            }
        }
        stage('debug docker') {
    steps {
        sh '''
        whoami
        id
        groups
        docker --version
        docker ps
        ls -l /var/run/docker.sock
        '''
    }
}
        stage('build docker image') {
            steps{
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }
        stage('upload docker image') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'DockerHub',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_TOKEN'
        )]) {
            sh '''
            echo $DOCKER_TOKEN | docker login -u $DOCKER_USER --password-stdin
            docker push $IMAGE_NAME:$BUILD_NUMBER
            '''
        }
    }
}
        stage('deploy to production') {
            steps {
                sh 'docker run -d -p 5000:5000 $IMAGE_NAME:$BUILD_NUMBER'
            }
        }
    }
}
