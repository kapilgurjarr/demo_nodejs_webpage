pipeline {
    agent any 
    tools {
        nodejs 'nodejs-10'   
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/kapilgurjarr/demo_nodejs_webpage.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('OWASP Dependencies Check') {
            steps {
                script {
                    try {
                        dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                        dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    // Assuming Docker is properly configured and running on the Jenkins agent
                    withDockerRegistry(credentialsId: '6f856e59-dd9e-43ee-bd23-f1741b4dbb6d', toolName: 'docker') {
                        sh 'docker build -t demonodejs .'
                        sh 'docker tag demonodejs kapilgurjar/testing:latest'
                        sh 'docker push kapilgurjar/testing:latest'
                    } 
                }
            }
        }
        
        stage('Docker Deploy') {
            steps {
                script {
                    // Assuming Docker is properly configured and running on the Jenkins agent
                    withDockerRegistry(credentialsId: '6f856e59-dd9e-43ee-bd23-f1741b4dbb6d', toolName: 'docker') {
                        sh 'docker run -d --name nodejs1 -p 8081:8081 kapilgurjar/testing:latest'
                    } 
                }
            }
        }
    }
}
