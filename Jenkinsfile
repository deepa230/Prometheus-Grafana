pipeline {
    agent any
  environment {
    PATH = "/usr/local/bin:$PATH"
  }
    stages {
        stage('Deploy to K8s') {
            steps {
                sshagent(['Kubernetes']) {
                    sh '''
                    
                    scp -o StrictHostKeyChecking=no ./mongodb.yaml ubuntu@3.109.48.24 /home/ubuntu
                    scp -o StrictHostKeyChecking=no ./values.yaml ubuntu@3.109.48.24:/home/ubuntu
                    ssh -t ubuntu@3.109.48.24 /bin/bash << 'EOF'
                    kubectl apply -f mongodb.yaml 
                    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
                    helm repo update
                    helm install prometheus prometheus-community/kube-prometheus-stack
                    kubectl patch svc prometheus-kube-prometheus-prometheus -p '{"spec": {"type": "NodePort"}}'
                    kubectl patch svc prometheus-grafana -p '{"spec": {"type": "NodePort"}}'
                    helm install mongodb-exporter prometheus-community/prometheus-mongodb-exporter -f values.yaml
EOF
                     '''
                    }
            }
        }
        
    }
}
