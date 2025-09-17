pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
  containers:
  - name: docker
    image: docker:24.0.7
    command: ["cat"]
    tty: true
    securityContext:
      privileged: true
    volumeMounts:
    - name: docker-sock
      mountPath: /var/run/docker.sock
  """
        }
    }

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'stage', 'prod'],
            description: 'Select the deployment environment'
        )
    }

    stages {
        stage('Clone repository') {
            steps {
                container('docker') {
                    script {
                        def repoUrl = 'https://github.com/venkat0007/maven-repo.git'
                        def branchName = 'main'
                        checkout([$class: 'GitSCM',
                                  branches: [[name: "refs/heads/${branchName}"]],
                                  userRemoteConfigs: [[url: repoUrl]]])
                    }
                }
            }
        }
        stage("building an image") {
            steps {
                container('docker') {
                    script {
                        sh "docker version"
                        sh "docker build -t backend-api:${params.tag} ."
                    }
                }
            }
        }
        stage("pushing the image to dockerhub") {
            steps {
                container('docker') {
                    script {
                        // TODO: replace 'dockerhub-creds' with your Jenkins credentials ID
                        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                          usernameVariable: 'DOCKER_USER',
                                                          passwordVariable: 'DOCKER_PASS')]) {
                            sh """
                                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                                docker tag backend-api:${params.tag} venkat12/backend-api:${params.tag}
                                docker push venkat12/backend-api:${params.tag}
                            """
                        }
                    }
                }
            }
        }
    }
}
