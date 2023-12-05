pipeline {
    agent {
        label "opex-dev"
    }

    environment {
        TAG = 'dev'
        PREFERENCES = "preferences.yml"
        PANEL_PASS = credentials("opex-dev-v-panel-secret")
        BACKEND_USER = credentials("opex-dev-v-backend-secret")
        SMTP_PASS = credentials("opex-dev-smtp-secret")
        DB_USER = 'opex'
        DB_PASS = credentials("opex-dev-db-secret")
        DB_READ_ONLY_USER = 'opex_reader'
        DB_READ_ONLY_PASS = credentials("opex-dev-db-reader-secret")
        KEYCLOAK_ADMIN_USERNAME = credentials("opex-dev-keycloak-admin-username")
        KEYCLOAK_ADMIN_PASSWORD = credentials("opex-dev-keycloak-admin-password")
        OPEX_ADMIN_KEYCLOAK_CLIENT_SECRET = credentials("opex-dev-opex-admin-keycloak-client-secret")
        //VANDAR_API_KEY = credentials("vandar-api-key")
        //FILEBEAT_API_KEY = credentials("filebeat-api-key")
    }

    stages {
        stage('Copy config files') {
            steps {
                configFileProvider([
                    configFile(fileId: 'preferences-dev.yml', variable: 'PREFERENCES_YML')
                ]) {
                    sh 'cp -f $PREFERENCES_YML ./preferences.yml'
                }
            }
        }
        stage('Deploy Swarm') {
            steps {
                sh 'docker stack deploy \
                        -c docker-stack.core.yml \
                        -c docker-stack.core-dev.yml \
                           opex-dev'
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
