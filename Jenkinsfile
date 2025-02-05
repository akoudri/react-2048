pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'eaa523cf-1138-4be1-84e6-dcc8d66cc4ba'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm run test
                    npm run test-coverage
                '''
            }
        }
        stage('Deploy Staging') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=out
                '''
            }
        }
        stage('Deploy Production') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify deploy --dir=out --prod
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'test-report.html'
            archiveArtifacts artifacts: 'out/**'
        }
    }
}