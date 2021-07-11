pipeline {
    agent any
  environment {
    PATH = "/usr/local/bin:$PATH"
  }
    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', credentialsId: 'Git', url: 'https://github.com/deepa230/Prometheus-Grafana.git'
            }
        }
        stage('Deploy to K8s') {
            steps {
                sshagent(['Kubernetes']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no /home/ubuntu/linux-amd64/helm ubuntu@13.126.18.218:/home/ubuntu
                    scp -o StrictHostKeyChecking=no /home/ubuntu/mongodb.yaml ubuntu@13.126.18.218:/home/ubuntu
                    scp -o StrictHostKeyChecking=no /home/ubuntu/values.yaml ubuntu@13.126.18.218:/home/ubuntu
                    ssh -t ubuntu@13.126.18.218 /bin/bash << 'EOF' 
                    sudo mv ./helm /usr/local/bin/ 
                    sudo chmod +x /usr/local/bin/helm 
                    export PATH=/usr/local/bin/:$PATH
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
