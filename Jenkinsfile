pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/Kevinkimanzi/containerizing-app.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=to-do \
                    -Dsonar.projectKey=to-do'''
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                    sh "docker build -t to-do ."
                    sh "docker tag to-do kevinkimanzi4/to-do:latest "
                    sh "docker push kevinkimanzi4/to-do:latest "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image kevinkimanzi4/to-do:latest > trivyimage.txt" 
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d  kevinkimanzi4/to-do:latest'
            }
        }
        
    }   
}