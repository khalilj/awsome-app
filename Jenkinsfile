pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                docker.build("awesome-app:${BRANCH_NAME}-${env.BUILD_NUMBER}")
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}