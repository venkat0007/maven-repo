pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
                script {
                    // Define the repository URL
                    def repoUrl = 'https://github.com/venkat0007/maven-repo.git'

                    // Define the branch name
                    def branchName = 'main'

                    // Checkout the repository with the specified branch
                    checkout([$class: 'GitSCM', 
                              branches: [[name: "refs/heads/${branchName}"]],
                              userRemoteConfigs: [[url: repoUrl]]])
                }
            }
        }
       stage("building an image")
       {
           steps {
                script {

                        sh 'docker build -t backend:1 .'
                    }
                }
           
           }
	   
    stage("pushing the image to dockerhub")
       {
           steps {
                script {
                    

                        sh '''
                            docker tag backend:1 venkat0007/backend:1
			    docker push venkat0007/backend:1
                        '''
                    }
                }
           }
	   }

       }
    }
