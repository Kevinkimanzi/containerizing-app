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
                git branch: 'main', url: 'https://github.com/Kevinkimanzi/containerizing-app'
            }
        }
        stage("Sonarqube Analysis") {
    steps {
        withSonarQubeEnv('sonar-server') {
            echo 'Running SonarQube analysis'
            sh '''echo "SCANNER_HOME: $SCANNER_HOME"
                $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=to-do \
                -Dsonar.projectKey=to-do'''
            echo 'SonarQube analysis completed'
        }
    }
}

        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}