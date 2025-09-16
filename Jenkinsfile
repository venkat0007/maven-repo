// Use only the docker:dind image as your pod's container
pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:24.0.7-dind
    command:
    - dockerd-entrypoint.sh
    args:
    - --host=tcp://0.0.0.0:2375
    - --host=unix:///var/run/docker.sock
    tty: true
    securityContext:
      privileged: true
    env:
    - name: DOCKER_HOST
      value: tcp://localhost:2375
"""
        }
    }
    parameters {
        string(name: 'tag', defaultValue: 'new', description: 'tagname')
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
                        sh "docker build -t backend-api:${params.tag} ."
                    }
                }
            }
        }
        stage("pushing the image to dockerhub") {
            steps {
                container('docker') {
                    script {
                        sh """
                            docker tag backend-api:${params.tag} venkat0007/backend-api:${params.tag}
                            docker push venkat0007/backend-api:${params.tag}
                        """
                    }
                }
            }
        }
    }
}
