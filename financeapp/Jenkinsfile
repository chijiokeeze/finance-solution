pipeline {
    agent any
    stages {
        stage('Download Source Code') {
            steps {
                git 'https://github.com/chijiokeeze/finance-solution.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'SonarQube', credentialsId: 'jenkins-token') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Upload War file to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'htech-finance-app', 
                        classifier: '', 
                        file: 'target/htech-finance-app-1.0-SNAPSHOT.jar', 
                        type: 'jar'
                    ]
                ], 
                    credentialsId: 'Nexus-credentials', 
                    groupId: 'com.htech', 
                    nexusUrl: '54.90.134.201:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'HTech-FinanceApp', 
                    version: '0.3'
                }
            }        
            stage('Dockerize') {
                steps {
                    withCredentials([usernamePassword(
                        credentialsId: 'Docker-credentials', 
                        passwordVariable: 'PASSWD', 
                        usernameVariable: 'USER')]) {
                    sh 'docker build -t cj15/htech-finance-app:latest .'
                    sh 'docker push cj15/htech-finance-app:latest'
                    }
                }
            }
    }
}
