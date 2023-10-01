pipeline {
    agent any

    stages('Deploy') {
        stage('Deploy Swarm') {
            environment {
                TAG = 'latest'
                PREFERENCES = "preferences.yml"
                PANEL_PASS = credentials("v-panel-secret")
                BACKEND_USER = credentials("v-backend-secret")
                SMTP_PASS = credentials("smtp-secret")
                DB_USER = 'opex'
                DB_PASS = credentials("db-secret")
                DB_READ_ONLY_USER = 'opex_reader'
                DB_READ_ONLY_PASS = credentials("db-reader-secret")
                KEYCLOAK_ADMIN_USERNAME = credentials("keycloak-admin-username")
                KEYCLOAK_ADMIN_PASSWORD = credentials("keycloak-admin-password")
                OPEX_ADMIN_KEYCLOAK_CLIENT_SECRET = credentials("opex-admin-keycloak-client-secret")
                VANDAR_API_KEY = credentials("vandar-api-key")
                FILEBEAT_API_KEY = credentials("filebeat-api-key")
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
                    configFile(fileId: 'preferences-demo.yml', variable: 'PREFERENCES_YML'),
                    configFile(fileId: 'whitelist.txt', variable: 'WHITELIST_TXT')
                ]) {
                    sh 'cp -f $PREFERENCES_YML ./preferences.yml'
                    sh 'cp -f $WHITELIST_TXT ./whitelist.txt'
                }
                sh 'docker stack deploy \
                        -c docker-stack.yml \
                        -c docker-stack.payment.yml \
                        -c docker-stack.chain-scanner.yml \
                        -c docker-stack.chain-scanner-demo.yml \
                        -c docker-stack.ui.yml \
                        -c docker-stack.backup.yml \
                        -c docker-stack.reverse-proxy.yml \
                        -c docker-stack.monitoring.yml \
                        -c docker-stack.demo.yml \
                           opex-demo'
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
            reposSource       : [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/opexdev/server-stack"],
            contextSource     : [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
            errorHandlers     : [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
            statusResultSource: [$class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]]]
    ])
}
