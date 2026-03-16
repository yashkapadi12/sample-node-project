pipeline {
    agent {label 'master'}

    environment {
        PIPELINE_NAME = "${params.PIPELINE_NAME}"
    }

    options {
        withCredentials([
            string(credentialsId: 'DOCKER_PASSWORD', variable: 'DOCKER_PASSWORD'),
            string(credentialsId: 'DOCKER_LOGIN', variable: 'USERNAME'),
        ])
    }


    stages {
        

        stage('Cloning the Project'){
            steps{
                script{
                    try{
                        git branch: "dev", credentialsId: 'git', url: "git@github.com:yashkapadi12/sample-node-project.git"
                    }
                    catch(Exception e)
                    {
                        echo "FAILED ${e}"
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }
        

        stage('Building the image') {
            steps {
                script{
                    try{
                        // Define Docker variables
                        def dockerImage = 'sample-node-project'
                        
                        // Build Docker image
                        sh "docker build -t ${dockerImage} ."
                        
                        // Login to Docker Hub
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${USERNAME} --password-stdin"
                        
                        // Push Docker image
                        sh "docker push ${dockerImage}"
                        
                        // Logout from Docker Hub
                        sh "docker logout"
                        
                    }
                    catch(Exception e) {
                        echo "FAILED ${e}"
                        throw e
                    }
                }
            }
        }
        
        stage ('Deploying the container') {
            steps {
                script {
                    try {
                        // Deploy the container
                        sh "docker run -d -p 3000:3000 --name sample-node-project ${dockerImage}"
                    }
                    catch(Exception e) {
                        echo "FAILED ${e}"
                        throw e
                    }
                }
            }
        }
    }
}