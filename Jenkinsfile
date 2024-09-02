pipeline {
    agent any

    environment {
        GIT_REPO = 'goelvansh/Buyfy' // GitHub repo in the format 'owner/repo'
        GIT_CREDENTIALS_ID = 'git-pr' // Jenkins credentials ID for GitHub
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Example build command
                    sh 'echo Build stage'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run unit tests
                    sh './gradlew test'
                }
            }
            post {
                always {
                    junit '**/build/test-results/**/*.xml'
                }
            }
        }

        stage('Static Analysis') {
            steps {
                script {
                    sh './gradlew check'
                }
            }
        }
    }

    post {
        success {
            script {
                def commitHash = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                setBuildStatus("Build succeeded", "SUCCESS")
            }
        }
        failure {
            script {
                def commitHash = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                setBuildStatus("Build failed", "FAILURE")
            }
        }
    }
}

void setBuildStatus(String message, String state) {
    try {
        step([
            $class: "GitHubCommitStatusSetter",
            reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/${env.GIT_REPO}.git"],
            contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
            errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
            statusResultSource: [$class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]]]
        ])
    } catch (Exception e) {
        echo "Failed to set GitHub commit status: ${e.message}"
    }
}

