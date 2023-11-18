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
                withCredentials([
                    file(credentialsId: 'private.pem', variable: 'PRIVATE'),
                    file(credentialsId: 'opex.dev.crt', variable: 'PUBLIC')
                ]) {
                    sh 'cp -f $PRIVATE ./private.pem'
                    sh 'cp -f $PUBLIC ./opex.dev.crt'
                }
                sh "docker stack deploy -c docker-stack.nginx.yml opex-dev-nginx"
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
