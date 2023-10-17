pipeline {
    agent any

    stages {
        stage('Check for Merge Commit') {
            steps {
                script {
                    def isMergeCommit = sh(script: 'git rev-parse HEAD^2', returnStatus: true) == 0
                    if (!isMergeCommit) {
                        currentBuild.result = 'ABORTED'
                        error('This is not a merge commit. Aborting the build.')
                    }
                }
            }
        }

        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Static Code Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarQube-pipeline';
                    withSonarQubeEnv('SonarQube-pipeline') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def app = docker.build("maximdove/my-app:${env.BUILD_ID}")
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        def app = docker.image("maximdove/my-app:${env.BUILD_ID}")
                        app.push('latest')
                        app.push("${env.BUILD_ID}")
                    }
                }
            }
        }
    }
}
