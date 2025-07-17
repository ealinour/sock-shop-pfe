pipeline {
    agent any

    stages {
        stage('Clone repo') {
            steps {
                git branch: 'master', url: 'https://github.com/ealinour/sock-shop-pfe.git'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f ./deploy/kubernetes/manifests/'
            }
        }
    }

    post {
        success {
            echo '✅ Déploiement réussi !'
        }
        failure {
            echo '❌ Déploiement échoué.'
        }
    }
}
