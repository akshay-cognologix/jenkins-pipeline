pipeline {

  agent {
    kubernetes {
      defaultContainer 'docker'
      yamlFile 'pod-template/podtemplate.yaml'
    }
  }
 stages {
    stage('Clone innive repository and build image') {
      steps {
        sh 'mkdir innive-repo; ls -lh'
        dir ('innive-repo') {
          git branch: 'main',
            credentialsId: 'github-creds',
            url: 'git@github.com:Cognologix/Cognologix-AISD-Airbyte.git'
        }
        sh '''
          ls -lRth; pwd
          find /home/jenkins/ -name "Cognologix-AISD-Airbyte"
          cd innive-repo/Cognologix-AISD-Airbyte/innive_airflow
          sh buildImage.sh edfi-airflow:2.2.3-v${BUILD_NUMBER}
          docker images
          cd -
          '''
      }
    }
/*    stage('Create Docker image') {
      steps {
        container('docker') {
            sh """
              cd /home/jenkins/agent/workspace/test-airflow2/innive-repo/Cognologix-AISD-Airbyte/innive_airflow
              sh buildImage.sh edfi-airflow:2.2.3-v${BUILD_NUMBER}
              docker images
              cd -
              """
        }
      } 
    }*/
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
