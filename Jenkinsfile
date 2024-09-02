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
                githubNotify(
                    credentialsId: "${env.GIT_CREDENTIALS_ID}",
                    status: 'SUCCESS',
                    description: 'Build succeeded',
                    context: 'ci/jenkins/build-status',
                    sha: commitHash,
                    repo: "${env.GIT_REPO}",
                    account: 'goelvansh'
                )
            }
        }
        failure {
            script {
                def commitHash = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                githubNotify(
                    credentialsId: "${env.GIT_CREDENTIALS_ID}",
                    status: 'FAILURE',
                    description: 'Build failed',
                    context: 'ci/jenkins/build-status',
                    sha: commitHash,
                    repo: "${env.GIT_REPO}",
                    account: 'goelvansh'
                )
            }
        }
    }
}
