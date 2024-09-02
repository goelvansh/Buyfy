void setBuildStatus(String message, String state) {
    step([
        $class: "GitHubCommitStatusSetter",
        reposSource: [
            $class: "ManuallyEnteredRepositorySource", 
            url: "https://github.com/goelvansh/Buyfy"
        ],
        contextSource: [
            $class: "ManuallyEnteredCommitContextSource", 
            context: "ci/jenkins/build-status"
        ],
        errorHandlers: [
            [$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]
        ],
        statusResultSource: [
            $class: "ConditionalStatusResultSource", 
            results: [
                [$class: "AnyBuildResult", message: message, state: state]
            ]
        ]
    ]);
}

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
        setBuildStatus("Build succeeded", "SUCCESS");
    }
    failure {
        setBuildStatus("Build failed", "FAILURE");
    }
    }
}
