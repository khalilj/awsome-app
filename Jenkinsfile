pipeline {
    agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: some-label-value
spec:
  containers:
  - name: docker
    image: docker:latest
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: "/var/run/docker.sock"
      name: "volume-0"
      readOnly: false
  - name: helm
    image: alpine/helm:2.14.0
    command:
    - cat
    tty: true
  volumes:
  - hostPath:
      path: "/var/run/docker.sock"
    name: "volume-0"
"""
    }
  }

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Building....'
                    tag = "${BRANCH_NAME}-${env.BUILD_NUMBER}"
                    container('docker') {
                        image = docker.build("khalilj/awesome-app:${tag}")
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    container('docker') {
                        image.inside {
                            echo "Testing..."
                        }
                    }
                }
            }
        }
        stage('Push to Docker Registry') {
            steps {
                script {
                    echo "Pushing image..."
                    container('docker') {
                        withDockerRegistry([ credentialsId: "docker-hub", url: "" ]) {
                            image.push()
                            if (BRANCH_NAME == 'master') {
                                image.push('latest')
                            }
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
                            sh "helm upgrade --install --force --namespace toluna-system --set image.tag=${tag} awesome-app-ci helm/"
                        }
                    }
                }
            }
        }
    }
}