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

        stage('Deploy Monitoring Stack') {
            steps {
                echo '📦 Déploiement de Prometheus et Grafana via Helm...'
                sh '''
                    # Ajouter les repos Helm s’ils ne sont pas encore ajoutés
                    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts || true
                    helm repo add grafana https://grafana.github.io/helm-charts || true
                    helm repo update

                    # Créer namespace monitoring si nécessaire
                    kubectl create namespace monitoring --dry-run=client -o yaml | kubectl apply -f -

                    # Déployer Prometheus
                    helm upgrade --install prometheus prometheus-community/prometheus \
                      --namespace monitoring

                    # Déployer Grafana
                    helm upgrade --install grafana grafana/grafana \
                      --namespace monitoring \
                      --set adminPassword=admin \
                      --set service.type=NodePort
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Déploiement réussi, y compris la stack de monitoring !'
        }
        failure {
            echo '❌ Déploiement échoué.'
        }
    }
}
