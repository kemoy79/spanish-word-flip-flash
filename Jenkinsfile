pipeline {
    agent any
    
    options {
        ansiColor('xterm')
    }
 
    stages {
        stage('build') {
            steps {
                script {
                    docker.image('node:22-alpine').inside {
                        sh 'npm ci'
                        sh 'npm run build'
                    }
                }
            }
        }
 
        stage('test') {
            steps {
                script {
                    docker.image('node:22-alpine').inside {
                        sh 'npx vitest run --reporter=verbose'
                    }
                }
            }
        }
 
        stage('deploy') {
            steps {
                script {
                    docker.image('alpine').inside {
                        echo 'Mock deployment was successful!'
                    }
                }
            }
        }
    }
}