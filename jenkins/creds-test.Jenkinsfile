#!/usr/bin/env groovy
@Library('jenkins-shared-library') _

node('docker-agent') {
    stage('Test Docker Login') {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
            sh '''
                echo "🔐 Logging in to Docker Hub..."
                echo "$DOCKERHUB_PASSWORD" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                if docker info | grep -q "Username: $DOCKERHUB_USERNAME"; then
                  echo "✅ Docker login successful as $DOCKERHUB_USERNAME"
                else
                  echo "❌ Docker login failed"
                  exit 1
                fi
                echo "🧼 Removing Docker credentials..."
                rm -f ~/.docker/config.json
            '''
        }
    }

    stage('Test GitHub Access') {
        withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
            sh '''
                echo "🔍 Testing GitHub repo access..."
                git config --global credential.helper store
                echo "https://${GIT_USER}:${GIT_PASS}@github.com" > ~/.git-credentials
                git ls-remote https://github.com/lolek1979/fullstackapp.git || {
                    echo "❌ GitHub access failed"
                    exit 1
                }
                echo "✅ GitHub access OK"
                rm -f ~/.git-credentials
            '''
        }
    }

    stage('Test Kubernetes via Token (dynamic kubeconfig)') {
        def kubeconfig = kubernetes.getKubeconfigFromServiceAccount()
        writeFile file: '.kube/config', text: kubeconfig

        sh '''
            mkdir -p ~/.kube
            cp .kube/config ~/.kube/config
            kubectl get ns -A
        '''
    }

    stage('Test Argo CD Access') {
        withCredentials([string(credentialsId: 'argocd-token', variable: 'ARGO_TOKEN')]) {
            sh '''
                echo "📋 Listing Argo CD apps..."
                argocd app list --auth-token "$ARGO_TOKEN" --grpc-web --insecure --server argocd.k8s.orb.local
            '''
        }
    }
}