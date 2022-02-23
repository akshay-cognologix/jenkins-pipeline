pipeline {

  agent {
    kubernetes {
      defaultContainer 'docker'
      yamlFile 'resources/agentPodTemplate.yaml'
    }
  }
 stages {
   git branch: 'main', credentialsId: 'github-creds', url: 'git@github.com:Cognologix/Cognologix-AISD-Airbyte.git'
    stage('Create Docker image') {
      container('docker') {
          sh """
            cd innive_airflow
            sh buildImage.sh edfi-airflow:2.2.3-v${BUILD_NUMBER}
            docker images
            """
      }
    }
   git branch: 'main', credentialsId: 'github-creds', url: 'git@github.com:Cognologix/Cognologix-AISD-Airbyte.git'
    stage('Run kubectl') {
      container('kubectl') {
        sh "kubectl get pods -n jenkins"
      }
    }
    stage('Run helm') {
      container('helm') {
        sh "helm list"
      }
    }
 }
}
