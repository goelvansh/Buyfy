pipeline {
    agent any

    environment {
        GIT_REPO = 'goelvansh/Buyfy' 
        GIT_CREDENTIALS_ID = 'git-pr' 
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
                withCredentials([usernamePassword(credentialsId: "${env.GIT_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    def commitHash = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    echo "Commit Hash: ${commitHash}"
                    setBuildStatus("Build succeeded", "SUCCESS", commitHash)
                }
            }
        }
        failure {
            script {
                withCredentials([usernamePassword(credentialsId: "${env.GIT_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    def commitHash = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    echo "Commit Hash: ${commitHash}"
                    setBuildStatus("Build failed", "FAILURE", commitHash)
                }
            }
        }
    }
}

void setBuildStatus(String message, String state, String commitHash) {
    try {
        withCredentials([usernamePassword(credentialsId: "${env.GIT_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
            echo "Setting GitHub commit status for repo: ${env.GIT_REPO}"
            step([
                $class: "GitHubCommitStatusSetter",
                reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${env.GIT_REPO}.git"],
                contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
                commitShaSource: [$class: "ManuallyEnteredShaSource", sha: commitHash],
                errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
                statusResultSource: [$class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]]]
            ])
            echo "Successfully set GitHub commit status to ${state}."
        }
    } catch (Exception e) {
        echo "Failed to set GitHub commit status: ${e.message}"
    }
}
