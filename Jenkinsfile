pipeline {

  agent {
    kubernetes {
      defaultContainer 'docker'
      yamlFile 'resources/agentPodTemplate.yaml'
    }
  }
 stages {
    stage('Clone innive repository') {
      steps {
        dir ('innive-repo') {
          git branch: 'main',
            credentialsId: 'github-creds',
            url: 'git@github.com:Cognologix/Cognologix-AISD-Airbyte.git'
        }
      }
    }
    stage('Create Docker image') {
      steps {
        container('docker') {
            sh """
              cd innive-repo/Cognologix-AISD-Airbyte/innive_airflow
              sh buildImage.sh edfi-airflow:2.2.3-v${BUILD_NUMBER}
              docker images
              """
        }
      } 
    }
    stage('Run kubectl') {
      steps {
        container('kubectl') {
          sh "kubectl get pods -n jenkins"
        }
      }
    }
    stage('Run helm') {
      steps {
        container('helm') {
          sh "helm list"
        }
      }
    }
 }
}
