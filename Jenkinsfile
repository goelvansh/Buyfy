pipeline {
    agent any

    environment {
        GIT_REPO = 'goelvansh/Buyfy' // GitHub repo in the format 'owner/repo'
        GIT_CREDENTIALS_ID = 'git-pr' // Jenkins credentials ID for GitHub access (username/password or token)
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
        // Construct the API request using credentials
        def apiUrl = "https://api.github.com/repos/${env.GIT_REPO}/statuses/${commitHash}"
        def auth = "${env.GIT_USERNAME}:${env.GIT_PASSWORD}".bytes.encodeBase64().toString()

        sh """
        curl -X POST -H "Authorization: Basic ${auth}" -H "Accept: application/vnd.github.v3+json" -H "Content-Type: application/json" \
        -d '{"state": "${state}", "description": "${message}", "context": "ci/jenkins/build-status"}' \
        ${apiUrl}
        """
        echo "Successfully set GitHub commit status to ${state}."
    } catch (Exception e) {
        echo "Failed to set GitHub commit status: ${e.message}"
    }
}
