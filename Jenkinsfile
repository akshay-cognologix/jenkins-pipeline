pipeline {

  agent {
    kubernetes {
      defaultContainer 'docker'
      yamlFile 'pod-template/podtemplatenew.yaml'
    }
  }
 stages {
    stage('Clone innive repository and build image') {
      steps {
        sh 'mkdir innive-repo; chown 1000:1000 innive-repo'
        dir ('innive-repo') {
          git branch: 'main',
            credentialsId: 'github-creds',
            url: 'git@github.com:Cognologix/Cognologix-AISD-Airbyte.git'
        }
        sh '''
        dockerConfig=\${DOCKER_CONFIG:-~/.docker}
        [ -d \${dockerConfig} ] && echo "Docker directory Exists" || mkdir -p \${dockerConfig}
        echo '{ "credsStore" : "ecr-login" }' > \${dockerConfig}/config.json
        chmod 600 \${dockerConfig}/config.json
        '''        
        sh '''
          cat \${dockerConfig}/config.json
          chown 1000:1000 -R innive-repo
          cd innive-repo/innive_airflow
          rm -rf innive_dbt/target/* innive_dbt/data/* innive_dbt/logs/* innive_dbt/dbt_packages/*
          #docker build . --network=host -f Dockerfile -t 516250856443.dkr.ecr.us-east-2.amazonaws.com/jenkins-airflow:airflow-edfi-v${BUILD_NUMBER} 
          docker images
          whoami
          #docker push 516250856443.dkr.ecr.us-east-2.amazonaws.com/jenkins-airflow:airflow-edfi-v${BUILD_NUMBER}
          docker push 516250856443.dkr.ecr.us-east-2.amazonaws.com/jenkins-airflow:airflow-edfi-v24
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
