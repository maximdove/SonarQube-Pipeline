pipeline {
    agent any
    triggers {
        githubPush branch: 'main'
    }
    stages {
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
                    def app = docker.build("my-app:${env.BUILD_ID}")
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        def app = docker.image("my-app:${env.BUILD_ID}")
                        app.push('latest')
                        app.push("${env.BUILD_ID}")
                    }
                }
            }
        }
    }
}
