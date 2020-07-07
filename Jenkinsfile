pipeline {
    agent {
        kubernetes {
            label 'docker'
            defaultContainer 'docker'
        }
    }

    stages {
        stage('Build') {
            steps {
                script {
                    image = docker.build("khalilj/awesome-app:${BRANCH_NAME}-${env.BUILD_NUMBER}")
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
                    withDockerRegistry([ credentialsId: "docker-hub", url: "" ]) {
                        image.push()
                        if (BRANCH_NAME.equals('master')) {
                            image.push('latest')
                        }
                    }
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