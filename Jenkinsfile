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
                    // On ajoute -DskipTests pour éviter de lancer les tests Spring Boot qui exigent une BDD externe
                    bat 'mvn test -DskipTests'
                }
            }
        }
        
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat 'npm install --force'
                    // On définit la variable d'environnement pour désactiver le contrôle de version de Node par Angular
                    bat 'set NG_SKIP_NODE_VERSION_CHECK=true && node ./node_modules/@angular/cli/bin/ng.js build'
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