pipeline {
    agent any

    options {
        ansiColor('xterm')
        disableConcurrentBuilds()
    }

    stages {

        /* ---------------------- BUILD ---------------------- */
        stage('build') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode false
                }
            }
            steps {
                checkout scm
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        /* ---------------------- TEST ----------------------- */
        stage('test') {
            parallel {

                /* -------- UNIT TESTS -------- */
                stage('unit tests') {
                    agent {
                        docker {
                            image 'node:20-alpine'
                            reuseNode false
                        }
                    }
                    steps {
                        checkout scm
                        sh 'npm ci'
                        sh 'npx vitest run --reporter=verbose'
                    }
                }

                /* -------- INTEGRATION TESTS -------- */
                stage('integration tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.58.2-jammy'
                            reuseNode false
                        }
                    }
                    steps {
                        checkout scm
                        sh 'rm -rf node_modules'
                        sh 'npm ci'

                        // Start server for integration tests
                        sh 'npx serve -s dist -l 4173 &'
                        sh 'sleep 3'

                        sh 'npx playwright test --config=playwright.config.ts'
                    }
                }
            }
        }

        /* ---------------------- DEPLOY ---------------------- */
        stage('deploy') {
            agent {
                docker {
                    image 'alpine'
                    reuseNode false
                }
            }
            steps {
                echo 'Mock deployment was successful!'
            }
        }

        /* ---------------------- E2E ------------------------- */
        stage('e2e') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.58.2-jammy'
                    reuseNode false
                }
            }
            environment {
                E2E_BASE_URL = 'https://the-internet.herokuapp.com/'
            }
            steps {
                checkout scm
                sh 'npm ci'
                sh 'npx playwright test'
            }
        }
    }
}