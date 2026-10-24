pipeline {
    agent any

    environment {
        // Définissez vos images (ou utilisez un registre comme Docker Hub ex: "votre-compte/spring-backend:1.0")
        BACKEND_IMAGE = "spring-backend:1.0"
        FRONTEND_IMAGE = "angular-frontend:1.0"
    }

    stages {
        stage('1. Checkout Code') {
            steps {
                // Récupère le code depuis votre dépôt GitHub
                checkout scm
            }
        }

        stage('2. Build Backend (Maven)') {
            steps {
                dir('backend') {
                    // Compilation du projet Spring Boot (ajustez selon votre OS: mvnw.bat pour Windows ou mvn)
                    bat 'mvn clean package -DskipTests'
                }
            }
        }

        stage('3. Build Frontend (Angular)') {
            steps {
                dir('frontend') {
                    // Installation des dépendances et build de production Angular
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('4. Build Docker Images') {
            steps {
                script {
                    // Construction de l'image Docker du Backend
                    bat "docker build -t ${BACKEND_IMAGE} ./backend"
                    
                    // Construction de l'image Docker du Frontend (avec Node 22/24 comme vu précédemment)
                    bat "docker build --no-cache -t ${FRONTEND_IMAGE} ./frontend"
                }
            }
        }

        stage('5. Load Images into Minikube') {
            steps {
                script {
                    // Si Jenkins et Minikube tournent sur la même machine Windows, 
                    // on charge les images directement dans le cluster Minikube
                    bat "minikube image load ${BACKEND_IMAGE}"
                    bat "minikube image load ${FRONTEND_IMAGE}"
                }
            }
        }

        stage('6. Deploy to Kubernetes') {
            steps {
                dir('k8s-app') {
                    // Application des manifestes Kubernetes dans l'ordre
                    bat 'kubectl apply -f namespace.yaml'
                    bat 'kubectl apply -f mysql/'
                    bat 'kubectl apply -f backend/'
                    // Utilisation de --validate=false pour éviter les soucis d'OpenAPI
                    bat 'kubectl apply -f frontend/ --validate=false'
                    
                    // Redémarrage forcé pour s'assurer que les pods prennent les dernières images
                    bat 'kubectl rollout restart deployment/backend -n devops'
                    bat 'kubectl rollout restart deployment/frontend -n devops'
                }
            }
        }
    }

    post {
            success {
                echo 'Pipeline executee avec succes ! Votre application est a jour sur Kubernetes.'
            }
            failure {
                echo 'Echec de la pipeline. Verifiez les etapes ci-dessus pour corriger cette erreur.'
            }
        }
}