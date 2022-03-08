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
          git branch: '${REPO_IMG_BRANCH}',
            credentialsId: 'github-creds',
            url: 'git@github.com:Cognologix/Cognologix-AISD-Airbyte.git'
        }
        sh '''
        dockerConfig=\${DOCKER_CONFIG:-~/.docker}
        [ -d \${dockerConfig} ] && echo "Docker directory Exists" || mkdir -p \${dockerConfig}
        echo '{ "credsStore" : "ecr-login" }' > \${dockerConfig}/config.json
        '''        
        sh '''
          cat /root/.docker/config.json
          chown 1000:1000 -R innive-repo
          cd innive-repo/innive_airflow
          export IMG_TAG=$(git rev-parse --short=7 HEAD)
          rm -rf innive_dbt/target/* innive_dbt/data/* innive_dbt/logs/* innive_dbt/dbt_packages/*
      	  echo ${IMG_TAG}
          #docker build . --network=host -f Dockerfile -t 516250856443.dkr.ecr.us-east-2.amazonaws.com/jenkins-airflow:airflow-edfi-v${IMG_TAG} 
          docker images
          #docker push 516250856443.dkr.ecr.us-east-2.amazonaws.com/jenkins-airflow:airflow-edfi-v${IMG_TAG}
          cd -
          '''
      }
    }

   stage('Deploy airflow') {
      steps {
        sh 'mkdir airflow-charts; chown 1000:1000 airflow-charts'
        dir ('airflow-charts') {
          git branch: '${REPO_HELM_BRANCH}',
            credentialsId: 'github-creds',
            url: 'git@github.com:Cognologix/airflow-helm-charts.git'
        }    
        sh '''
          chown 1000:1000 -R airflow-charts
          cd airflow-charts/
	        echo ${IMG_TAG}
          #helm upgrade --install -f values.yaml --set  defaultAirflowTag=airflow-edfi-v${IMG_TAG} --set logs.persistence.enabled=false -n test-airflow  airflow-test1  .
          helm ls
          cd -
          '''
      }
    }

    stage('Run kubectl') {
      steps {
         sh "kubectl get pods -n test-airflow"
      }
    }    
 }
}
