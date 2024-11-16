pipeline {
    agent any

    stages {
        stage('SCM Checkout') {
            steps {
                // Checkout the Git repository
                git url: 'https://github.com/Gokul-Prabakran1/my-app.git'
            }
        }

        stage('Compile-Package') {
            steps {
                // Use the Maven tool configured in Jenkins, with the tool name 'maven5'
                script {
                    def mvnHome = tool name: 'maven5', type: 'maven'
                    
                    // Run the Maven build command
                    sh "${mvnHome}/bin/mvn clean package"
                    
                    // Rename the WAR file after the build
                    sh 'mv target/myweb*.war target/newapp.war'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image with the tag 'gokul72/myweb:0.0.2'
                sh 'docker build -t gokul72/myweb:0.0.2 .'
            }
        }

        stage('Docker Image Push') {
            steps {
                // Use Jenkins credentials to login to Docker Hub
                withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                    sh "docker login -u gokul72 -p ${dockerPassword}"
                }

                // Push the Docker image to Docker Hub
                sh 'docker push gokul72/myweb:0.0.2'
            }
        }
        
        stage('Nexus Image Push') {
            steps {
                script {
                    // Log in to Nexus and push the Docker image
                    sh "docker login -u admin -p admin123 3.110.161.21:8083"
                    sh "docker tag gokul72/myweb:0.0.2 3.110.161.21:8083/gokul:1.0.0"
                    sh 'docker push 3.110.161.21:8083/gokul:1.0.0'
                }
            }
        }

        stage('Remove Previous Container') {
            steps {
                script {
                    // Try to remove the previous container if it exists
                    sh 'docker rm -f webcontainer || true'
                }
            }
        }

        stage('Docker Deployment') {
            steps {
                // Run a new Docker container
                sh 'docker run -d -p 8090:8080 --name webcontainer gokul72/myweb:0.0.2'
            }
        }
    }
}
