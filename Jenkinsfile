pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { 
                checkout scm 
            }
        }
        
        stage('Build Backend') {
            tools {
                maven 'Maven'
            }
            steps {
                dir('backend') {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Test Backend') {
            tools {
                maven 'Maven'
            }
            steps {
                dir('backend') {
                    bat 'mvn test'
                }
            }
        }
        
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat 'npm install --force'
                    // On utilise node directement pour lancer le script de build sans la vérification stricte du CLI
                    bat 'npm run build'
                }
            }
        }
        
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline executee avec succes !'
        }
        failure {
            echo 'Echec de la pipeline.'
        }
    }
}