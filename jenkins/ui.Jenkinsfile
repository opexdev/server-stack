def getTagEnvForBranch() {
    echo "Branch ${env.BRANCH_NAME}"
    if(env.BRANCH_NAME == 'main'){
        return 'latest'
    } else {
        return 'dev'
    }
}

pipeline {
    agent {
        label "opex-dev"
    }

    environment {
        TAG = 'dev'
    }

    stages {
        stage('Deploy Swarm') {
            steps {
                sh "docker stack deploy -c docker-stack.ui.yml opex-dev-ui"
            }
        }
    }

    post {
        always {
            echo 'One way or another, I have finished'
        }
        success {
            echo ':)'
        }
        unstable {
            echo ':/'
        }
        failure {
            echo ':('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}
