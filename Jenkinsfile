void setBuildStatus(String sha, String message, String state) {
    if (sha == null || sha.isEmpty()) {
        error "Commit SHA is null or empty. Cannot set build status."
    }
    
    def apiUrl = "https://api.github.com/repos/${env.GIT_REPO}/statuses/${sha}"
    def payload = [
        state: state,
        description: message,
        context: "ci/jenkins/build-status"
    ]

    def response = httpRequest(
        acceptType: 'APPLICATION_JSON',
        contentType: 'APPLICATION_JSON',
        httpMode: 'POST',
        url: apiUrl,
        authentication: env.GIT_CREDENTIALS_ID,
        requestBody: new groovy.json.JsonBuilder(payload).toString()
    )

    echo "Response: ${response}"
}

pipeline {
    agent any

    environment {
        GIT_REPO = 'goelvansh/Buyfy' // GitHub repo in the format 'owner/repo'
        GIT_CREDENTIALS_ID = 'git-pr' // Jenkins credentials ID for GitHub
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the code
                    checkout scm
                    env.COMMIT_SHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                    echo "Commit SHA: ${env.COMMIT_SHA}"
                }
            }
        }

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
            setBuildStatus(env.COMMIT_SHA, "Build succeeded", "success")
        }
        failure {
            setBuildStatus(env.COMMIT_SHA, "Build failed", "failure")
        }
    }
}
