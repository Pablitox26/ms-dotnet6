pipeline {
    agent any

    stages{
        stage('SCM') {
            steps {
                script {
                    properties([pipelineTriggers([pollSCM('* * * * *')])])
                }
               git branch: 'main', url: 'https://github.com/Pablitox26/ms-dotnet6.git'
            }
        }
        // stage('Build APP') {
            // steps {
            //     sh(script: 'ls' , returnStdout:true);
            //     sh(script: 'dotnetRestore' , returnStdout:true);
            //     sh(script: 'dotnetBuild' , returnStdout:true);
            //     sh(script: 'ddotnetTest' , returnStdout:true);
            // }
        // }
        stage('Docker build') {
            steps {
                sh(script: 'docker build -t pablobell/dotnet6v3 .' , returnStdout:true);
                sh(script: 'docker login --username $USERNAME_DOCKER --password $PASSWORK_DOCKER' , returnStdout:true);
                sh(script: 'docker push pablobell/dotnet6v3' , returnStdout:true);
            }
        }
        stage('Deploy AKS') {
            steps {
                sh(script: 'az aks get-credentials --resource-group devops --name clusterdevopspb & kubectl config get-contexts --kubeconfig=$KUBE_PATH_CONFIG' , returnStdout:true);
                sh(script: 'kubectl config use-context clusterdevopspb --kubeconfig=$KUBE_PATH_CONFIG' , returnStdout:true);
                sh(script: 'kubectl apply -f k8s.yml --kubeconfig=$KUBE_PATH_CONFIG' , returnStdout:true);
                sh(script: 'kubectl rollout restart deployment app-deployment --kubeconfig=$KUBE_PATH_CONFIG' , returnStdout:true);
            }
        }
    }
}
