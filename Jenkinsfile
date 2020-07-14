pipeline {

  agent {
    docker {
      image 'convisoappsec/flowcli:latest'
      args '-v /var/run/docker.sock:/var/run/docker.sock'
    }
  }

  environment {
    FLOW_API_KEY      = credentials('FLOW_API_KEY')
    FLOW_PROJECT_CODE = credentials('FLOW_PROJECT_CODE')
    FLOW_API_URL      = 'https://homologa.conviso.com.br'
  }

  stages {
    stage('AppSec_Flow') {
      steps {
        sh '''
            flow deploy create \
            -f env_vars with tag-tracker \
            sort-by time > created_deploy_vars
        '''

        sh '''
            source created_deploy_vars

            flow sast run \
            --start-commit "$FLOW_DEPLOY_PREVIOUS_VERSION_COMMIT" \
            --end-commit "$FLOW_DEPLOY_CURRENT_VERSION_COMMIT"
        '''
      }
    }
  }

  post {
      always {
        sh '''
            rm -f created_deploy_vars
        '''
    }
  }
}
