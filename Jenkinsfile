pipeline {
    agent {
        kubernetes {
            label 'generic-pod'
            defaultContainer 'docker'
        }
    }

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Building....'
                    image = docker.build("khalilj/awesome-app:${BRANCH_NAME}-${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    image.inside {
                        echo "Testing..."
                    }
                }
            }
        }
        stage('Push to Docker Registry') {
            steps {
                script {
                    echo "Pushing image..."
                    withDockerRegistry([ credentialsId: "docker-hub", url: "" ]) {
                        image.push()
                        if (BRANCH_NAME == 'master') {
                            image.push('latest')
                        }
                    }
                }
            }
        }
        stage('Deploy') {
            when { expression { BRANCH_NAME == 'master' } }

            steps {
                echo 'Deploying....'
                container('helm') {
                    withCredentials([file(credentialsId: 'K8S_KUBE_CONFIG', variable: 'kubeConfig')]){
                        script {
                            sh "mkdir ~/.kube && cp $kubeConfig ~/.kube/config"
                            sh "helm upgrade --install --force --namespace toluna-system awesome-app-ci helm/"
                        }
                    }
                }
            }
        }
    }
}