pipeline {
    agent any

    parameters {
        string(name: 'GIT_REPO', description: 'Git Repository URL', defaultValue: 'https://github.com/komalChauhan99/portfolio.git')
        string(name: 'GIT_BRANCH', description: 'Git Branch', defaultValue: 'master')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    def gitCheckout = checkout([$class: 'GitSCM', 
                        branches: [[name: params.GIT_BRANCH]], 
                        doGenerateSubmoduleConfigurations: false, 
                        extensions: [], 
                        submoduleCfg: [], 
                        userRemoteConfigs: [[url: params.GIT_REPO]]
                    ])
                    if (gitCheckout) {
                        currentBuild.result = 'SUCCESS'
                    } else {
                        currentBuild.result = 'FAILURE'
                        error("Git checkout failed")
                    }
                }
            }
        }

        stage('Deploy to Apache') {
            steps {
                script {
                    def sourceDir = "C:\Users\HP\Desktop\portfolio-main"
                    def targetDir = "C:\Apache24\htdocs"
                    def copyResult = bat(script: "xcopy /s /e /y \"$sourceDir\" \"$targetDir\"", returnStatus: true)
                    if (copyResult == 0) {
                        currentBuild.result = 'SUCCESS'
                    } else {
                        currentBuild.result = 'FAILURE'
                        error("Code deployment to Apache failed")
                    }
                }
            }
        }
    }

    post {
        failure {
            emailext subject: "Jenkins Build Failed: \${currentBuild.fullDisplayName}",
                     body: "The Jenkins build for \${currentBuild.fullDisplayName} has failed. Please investigate.",
                     recipientProviders: [culprits()]
        }
        success {
            emailext subject: "Jenkins Build Succeeded: \${currentBuild.fullDisplayName}",
                     body: "The Jenkins build for \${currentBuild.fullDisplayName} has succeeded.",
                     recipientProviders: [culprits()]
        }
    }
}
