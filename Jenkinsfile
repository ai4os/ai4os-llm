#!/usr/bin/groovy

// Simple Pipeline that only checks the metadata

pipeline {
    agent {
        label 'docker'
    }
    environment {
        METADATA_VERSION = "2.0.0"
    }
    stages {
        stage("Variable initialization") {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('AI4OS Hub metadata V2 validation (YAML)') {
            when {
                // Check if ai4-metadata.yml is present in the repository
                expression {fileExists("ai4-metadata.yml")}
            }
            agent {
                docker {
                    image 'ai4oshub/ci-images:python3.12'
                }
            }
            steps {
                script {
                    if (!fileExists("ai4-metadata.yml")) {
                        error("ai4-metadata.yml file not found in the repository")
                    }
                    if (fileExists("ai4-metadata.json")) {
                        error("Both ai4-metadata.json and ai4-metadata.yml files found in the repository")
                    }
                }
                script {
                    sh "ai4-metadata validate --metadata-version ${env.METADATA_VERSION} ai4-metadata.yml"
                }
            }
        }
        stage("License validation") {
            steps {
                script {
                    // Check if LICENSE file is present in the repository
                    if (!fileExists("LICENSE")) {
                        error("LICENSE file not found in the repository")
                    }
                }
            }
        }

    }

    post {
        always {
            cleanWs()
        }
    }
}