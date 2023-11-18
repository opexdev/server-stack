pipeline {
    agent {
        label "opex-dev"
    }

    stages {
        stage('Deploy Swarm') {
            environment {
                TAG = 'dev'
                PREFERENCES = "preferences.yml"
                PANEL_PASS = credentials("v-panel-secret-dev")
                BACKEND_USER = credentials("v-backend-secret-dev")
                SMTP_PASS = credentials("smtp-secret-dev")
                DB_USER = 'opex'
                DB_PASS = credentials("db-secret-dev")
                DB_READ_ONLY_USER = 'opex_reader'
                DB_READ_ONLY_PASS = credentials("db-reader-secret-dev")
                KEYCLOAK_ADMIN_USERNAME = credentials("keycloak-admin-username-dev")
                KEYCLOAK_ADMIN_PASSWORD = credentials("keycloak-admin-password-dev")
                OPEX_ADMIN_KEYCLOAK_CLIENT_SECRET = credentials("opex-admin-keycloak-client-secret-dev")
                VANDAR_API_KEY = credentials("vandar-api-key-dev")
                FILEBEAT_API_KEY = credentials("filebeat-api-key-dev")
            }
            steps {
                withCredentials([
                    file(credentialsId: 'private.pem', variable: 'PRIVATE'),
                    file(credentialsId: 'opex.dev.crt', variable: 'PUBLIC')
                ]) {
                    sh 'cp -f $PRIVATE ./private.pem'
                    sh 'cp -f $PUBLIC ./opex.dev.crt'
                }
                configFileProvider([
                    configFile(fileId: 'preferences-dev.yml', variable: 'PREFERENCES_YML'),
                    configFile(fileId: 'whitelist.txt', variable: 'WHITELIST_TXT')
                ]) {
                    sh 'cp -f $PREFERENCES_YML ./preferences.yml'
                    sh 'cp -f $WHITELIST_TXT ./whitelist.txt'
                }
                sh 'docker stack deploy \
                        -c docker-stack.yml \
                        -c docker-stack.backup.yml \
                        -c docker-stack.reverse-proxy.yml \
                        -c docker-stack.dev.yml \
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
