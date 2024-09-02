void setBuildStatus(String sha, String message, String state) {
    def apiUrl = "https://api.github.com/repos/${env.GIT_REPO}/statuses/${sha}"
    def payload = [
        state  : state,
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
        GIT_REPO = 'goelvansh/Buyfy' 
        GIT_CREDENTIALS_ID = 'git-pr' 
    }

    stages {
        stage('Build') {
            steps {
                script {
                    sh 'echo Build stage'
                }
            }
        }

        stage('Test') {
            steps {
                script {
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
