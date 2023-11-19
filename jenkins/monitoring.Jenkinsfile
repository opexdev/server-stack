pipeline {
    agent {
        label "opex-dev"
    }

    stages {
        environment {
            TAG = 'dev'
            REGISTRY_CREDS = credentials('docker-registry')
        }

        stage('Docker Login') {
            steps {
                echo "${env.OPEX_DOCKER_REGISTRY}"
                sh "docker login -u $REGISTRY_CREDS_USR -p $REGISTRY_CREDS_PSW ${env.OPEX_DOCKER_REGISTRY}"
            }
        }
        stage('Deploy Swarm') {
            steps {
                sh 'docker stack deploy -c docker-stack.monitoring.yml opex-dev-monitoring'
            }
        }
        stage('Docker Logout') {
            steps {
                echo "${env.OPEX_DOCKER_REGISTRY}"
                sh "docker logout ${env.OPEX_DOCKER_REGISTRY}"
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
