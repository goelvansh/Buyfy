void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/goelvansh/Buyfy.git"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}
pipeline {
    agent any
    // Triggers the pipeline on pull requests
    // triggers {
    //     githubPullRequests()
    //     githubPush()
    // }
    environment {
        // Set your repository and credentials here
        GIT_REPO = 'goelvansh/Buyfy' // Example: 'myorg/myrepo'
        GIT_CREDENTIALS_ID = 'github-id' // ID of credentials configured in Jenkins
    }
    stages {
        stage('Build') {
            steps {
                script {
                    // Add your build commands here
                    sh 'echo hiiii' // Example for a Gradle build
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run unit tests or other types of tests
                    sh './gradlew test' // Example for running tests with Gradle
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

