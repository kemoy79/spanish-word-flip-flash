pipeline {
    agent any

    options {
        ansiColor('xterm')
    }

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:25-alpine'
                }
            }
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        stage('test') {
            parallel {
                stage('unit tests') {
                    agent {
                        docker {
                            image 'node:25-alpine'
                            reuseNode true
                            customWorkspace 'ws-unit'
                        }
                    }
                    steps {
                        sh 'npm ci'
                        sh 'npx vitest run --reporter=verbose'
                    }
                }

                stage('integration tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.58.2-jammy'
                            reuseNode true
                            customWorkspace 'ws-integration'
                        }
                    }
                    steps {
                        sh 'git clean -fdx'
                        sh 'npm ci'
                        sh 'npx playwright test'
                    }
                }
            }
        }

        stage('deploy') {
            agent {
                docker {
                    image 'alpine'
                }
            }
            steps {
                echo 'Mock deployment was successful!'
            }
        }

        stage('e2e') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.58.2-jammy'
                    reuseNode true
                    customWorkspace 'ws-e2e-tests'
                }
            }
            environment {
                E2E_BASE_URL = 'https://spanish-cards.netlify.app/'
            }
            steps {
                sh 'npm ci'
                sh 'npx playwright test'
            }
        }
    }
}
