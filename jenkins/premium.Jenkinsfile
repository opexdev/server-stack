pipeline {
    agent {
        label "opex-dev"
    }

    stages {
        stage('Docker Login') {
            steps {
                echo "${env.OPEX_DOCKER_REGISTRY}"
                sh "docker login -u $REGISTRY_CREDS_USR -p $REGISTRY_CREDS_PSW ${env.OPEX_DOCKER_REGISTRY}"
            }
        }
        stage('Deploy Swarm') {
            environment {
                TAG = 'dev'
                PREFERENCES = "preferences.yml"
                BACKEND_USER = credentials("opex-dev-v-backend-secret")
                SMTP_PASS = credentials("opex-dev-smtp-secret")
                DB_USER = 'opex'
                DB_PASS = credentials("opex-dev-db-secret")
                DB_READ_ONLY_USER = 'opex_reader'
                DB_READ_ONLY_PASS = credentials("opex-dev-db-reader-secret")
            }
            steps {
                configFileProvider([
                    configFile(fileId: 'preferences-dev.yml', variable: 'PREFERENCES_YML')
                ]) {
                    sh 'cp -f $PREFERENCES_YML ./preferences.yml'
                }
                sh 'docker stack deploy -c docker-stack.premium.yml opex-dev-premium'
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
