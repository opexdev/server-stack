pipeline {
    agent any

    stages('Deploy') {
        stage('Deliver') {
            environment {
                OPEX_TAG = 'dev'
                PANEL_PASS = credentials("v-panel-secret-dev")
                BACKEND_USER = credentials("v-backend-secret-dev")
                SMTP_PASS = credentials("smtp-secret-dev")
                DB_USER = 'opex'
                DB_PASS = credentials("db-secret-dev")
                DB_BACKUP_USER = 'opex_backup'
                DB_BACKUP_PASS = credentials("db-backup-secret-dev")
                KEYCLOAK_ADMIN_URL = 'https://demo.opex.dev:8443/auth'
                KEYCLOAK_FRONTEND_URL = 'https://demo.opex.dev:8443/auth'
                KEYCLOAK_ADMIN_USERNAME = credentials("keycloak-admin-username-dev")
                KEYCLOAK_ADMIN_PASSWORD = credentials("keycloak-admin-password-dev")
                OPEX_ADMIN_KEYCLOAK_CLIENT_SECRET = credentials("opex-admin-keycloak-client-secret-dev")
                VANDAR_API_KEY = credentials("vandar-api-key-dev")
            }
            steps {
                withCredentials([
                    file(credentialsId: 'private.pem', variable: 'PRIVATE'),
                    file(credentialsId: 'opex.dev.crt', variable: 'PUBLIC')
                ]) {
                    sh 'cp -f $PRIVATE ./private.pem'
                    sh 'cp -f $PUBLIC ./opex.dev.crt'
                }
                sh 'docker stack deploy -c docker-stack.yml -c docker-stack.dev.yml dev-opex'
                sh 'docker service update dev-opex_nginx -d'
                sh 'docker image prune -f'
                sh 'docker network prune -f'
            }
        }
    }

    post {
        always {
            echo 'One way or another, I have finished'
        }
        success {
            echo ':)'
            setBuildStatus(":)", "SUCCESS")
        }
        unstable {
            echo ':/'
            setBuildStatus(":/", "UNSTABLE")
        }
        failure {
            echo ':('
            setBuildStatus(":(", "FAILURE")
        }
        changed {
            echo 'Things were different before...'
        }
    }
}

void setBuildStatus(String message, String state) {
    step([
            $class            : "GitHubCommitStatusSetter",
            reposSource       : [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/opexdev/OPEX-Swarm"],
            contextSource     : [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
            errorHandlers     : [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
            statusResultSource: [$class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]]]
    ])
}
