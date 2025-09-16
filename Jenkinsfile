pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:20.10-dind
    tty: true
    securityContext:
      privileged: true   # Required for DinD
    volumeMounts:
    - name: docker-graph-storage
      mountPath: /var/lib/docker
  volumes:
  - name: docker-graph-storage
    emptyDir: {}
"""
        }
    }
    parameters {
        string(name: 'tag', defaultValue: 'new', description: 'tagname')
    }
    stages {
        stage('Build & Push Docker Image') {
            steps {
                container('docker') {
                    sh """
                      dockerd-entrypoint.sh &   # start Docker daemon
                      sleep 10                  # wait for daemon
                      docker build -t backend-api:${params.tag} .
                      docker tag backend-api:${params.tag} venkat0007/backend-api:${params.tag}
                      docker push venkat0007/backend-api:${params.tag}
                    """
                }
            }
        }
    }
}
