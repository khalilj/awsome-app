pipeline {
    agent {
        kubernetes {
            label 'default'
            defaultContainer 'docker'
        }
    }

    stages {
        stage('Build') {
            steps {
                script {
                    image = docker.build("awesome-app:${BRANCH_NAME}-${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    image.inside {
                        echo "Testing"
                    }
                }
            }
        }
        stage('Push to Docker Registry') {
            steps {
                script {
                    image.push()
                    image.push('latest')
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}