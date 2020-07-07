pipeline {
    agent {
        kubernetes {
            yaml """
                apiVersion: v1
                kind: Pod
                spec:
                containers:
                - name: docker
                    image: docker:latest
                    command:
                    - cat
                    tty: true
                volumes:
                - name: dockersock
                    hostPath: /var/run/docker.sock
                    mountPath: /var/run/docker.sock
                """
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
                    customImage.inside {
                        echo "Testing"
                    }
                }
            }
        }
        stage('Push to Docker Registry') {
            steps {
                script {
                    customImage.push()
                    customImage.push('latest')
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