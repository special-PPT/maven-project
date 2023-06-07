pipeline {
    agent {
        label 'workernode1'
    }
        

    stages {
        stage('Checkout') {
            steps {
                // Get some code from a GitHub repository
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/special-PPT/maven-project.git']]])
            }
        }
        
        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn clean package"
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t webapp:v${BUILD_NUMBER} ."
            }
        }
        
        stage('Publsih to Docker Hub ') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub_cred', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        	    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                }
                sh "docker tag webapp:v${BUILD_NUMBER} robinwei/webapp:v${BUILD_NUMBER}"
                sh "docker push robinwei/webapp:v${BUILD_NUMBER}"
            }
        }
        
        stage('Create Container') {
            steps {
                sh "docker run --name webapp_v${BUILD_NUMBER} -p 8085:8080 -d webapp:v${BUILD_NUMBER}"
            }
        }    
    }
}



