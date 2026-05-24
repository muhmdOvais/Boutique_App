pipeline {
    agent any

    environment {
        AWS_REG = 'ap-northeast-1'
        CLUSTER_NAME = 'boutique-eks-cluster'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/muhmdOvais/Boutique_App.git'
            }
        }

        stage('Configure AWS & EKS') {
            steps {
                withCredentials([aws(credentialsId: 'aws-creds')]) {
                    sh """
                    aws eks update-kubeconfig --region ${AWS_REG} --name ${CLUSTER_NAME}
                    """
                }
            }
        }

        stage('Build & Push Docker Images') {
            steps {
                withCredentials([aws(credentialsId: 'aws-creds')]) {
                    sh """
                    cd Microservices
                    chmod +x docker_image_buid_push.sh
                    ./docker_image_buid_push.sh
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([aws(credentialsId: 'aws-creds')]) {
                    sh """
                    kubectl apply -f Microservices/kubernetes-manifests
                    """
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([aws(credentialsId: 'aws-creds')]) {
                    sh """
                    kubectl get pods
                    kubectl get svc
                    """
                }
            }
        }
    }

    post {
        always {
            sh 'docker system prune -f'
            cleanWs()
        }
    }
}
