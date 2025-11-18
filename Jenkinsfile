pipeline {
    agent any 

    environment {
        // Variable d'environnement pour le chemin de déploiement (comme demandé)
        DEPLOY_DIR = '/var/www/demodbs' 
        
        // ID du Credential Jenkins pour Azure DevOps (basé sur votre capture)
        CREDENTIAL_ID_GIT = 'azure_demodbs' 
        
        // URL de clonage du dépôt Azure DevOps
        REPO_URL = 'https://mondevsrv.visualstudio.com/DefaultCollection/DemoQuantech/_git/DemoQuantech' 
    }

    stages {
        stage('Nettoyage et Checkout Code') {
            steps {
                echo "Clonage du code depuis Azure DevOps..."
                // Utilise le Credential pour s'authentifier lors du clone
                withCredentials([usernamePassword(credentialsId: env.CREDENTIAL_ID_GIT, usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    git url: env.REPO_URL, 
                        branch: 'Filliale', // Assurez-vous que c'est la bonne branche
                        credentialsId: env.CREDENTIAL_ID_GIT 
                }
                // Nettoyer tous les anciens fichiers de build, sauf le dépôt cloné
                sh 'rm -rf target/*' 
            }
        }

        stage('Déploiement des Fichiers Statiques') {
            steps {
                script {
                    echo "Déploiement du contenu statique vers ${env.DEPLOY_DIR}..."

                    // 1. Créer le répertoire cible s'il n'existe pas
                    
                    sh "mkdir -p ${env.DEPLOY_DIR}"
                    
                    // 2. Copier les fichiers statiques (index.html, etc.)
                    sh "cp -r * ${env.DEPLOY_DIR}/" 
                    
                }
            }
        }
        
        stage('Recharger Nginx') {
            steps {
                echo "Rechargement de la configuration Nginx..."
                
                // Nécessite sudo sans mot de passe pour l'utilisateur Jenkins/ubuntu
                sh "sudo systemctl daemon-reload"
                sh "sudo systemctl restart nginx"
                
                echo "Déploiement terminé. L'application devrait être accessible sur le port 80 de votre serveur."
            }
        }
    }
}
