pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  volumes:
  - name: docker-graph-storage
    emptyDir: {}
  containers:
  - name: docker
    image: docker:24.0.7
    command: ["cat"]
    tty: true
    securityContext:
      privileged: true
    env:
    - name: DOCKER_HOST
      value: tcp://localhost:2375
  - name: dind
    image: docker:24.0.7-dind
    securityContext:
      privileged: true
    env:
    - name: DOCKER_TLS_CERTDIR
      value: ""                     # disable TLS so 2375 works
    args: ["--host=tcp://0.0.0.0:2375"]
    volumeMounts:
    - name: docker-graph-storage
      mountPath: /var/lib/docker
    ports:
    - containerPort: 2375
      name: dockerd
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
                        // Debug: check connection to DinD
                        sh "echo DOCKER_HOST=\$DOCKER_HOST"
                        sh "ps -ef | grep dockerd || true"
                        sh "curl -s http://localhost:2375/version || true"

                        // Check docker client and daemon
                        sh "docker version"

                        // Build image
                        sh "docker build -t backend-api:${params.tag} ."
                    }
                }
            }
        }
        stage("pushing the image to dockerhub") {
            steps {
                container('docker') {
                    script {
                        // Add docker login here if needed
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
