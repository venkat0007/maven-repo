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

<<<<<<< HEAD
                        sh 'docker build -t backend$tag .'
=======
                        sh 'docker build -t backend-api:1 .'
>>>>>>> 9cae32eb80d66adc9d3ee04c0d5737e98f33e021
                    }
                }
           
           }
	   
    stage("pushing the image to dockerhub")
       {
           steps {
                script {
                    

                        sh '''
<<<<<<< HEAD
                            docker tag backend:1 venkat0007/backend$tag
			    docker push venkat0007/backend$tag
=======
                            docker tag backend-api:1 venkat0007/backend-api:1
			    docker push venkat0007/backend-api:1
>>>>>>> 9cae32eb80d66adc9d3ee04c0d5737e98f33e021
                        '''
                    }
                }
           }
	   }

       }
    
