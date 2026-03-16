pipeline {
    agent any

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
                    sh '/Applications/Docker.app/Contents/Resources/bin/docker --version'
                    try{
                        // Define Docker variables
                        def dockerImage = 'yashkapadii/sample-node-project'
                        
                        // Build Docker image using absolute path
                        sh "/Applications/Docker.app/Contents/Resources/bin/docker build -t ${dockerImage} ."
                        
                        // Login to Docker Hub
                        sh "/Applications/Docker.app/Contents/Resources/bin/docker login -u ${USERNAME} --password-stdin <<< ${DOCKER_PASSWORD}"
                        
                        // Push Docker image
                        sh "/Applications/Docker.app/Contents/Resources/bin/docker push ${dockerImage}"
                        
                        // Logout from Docker Hub
                        sh "/Applications/Docker.app/Contents/Resources/bin/docker logout"
                        
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
                        // Deploy the container using absolute Docker path
                        sh "/Applications/Docker.app/Contents/Resources/bin/docker run -d -p 3000:3000 --name sample-node-project sample-node-project"
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