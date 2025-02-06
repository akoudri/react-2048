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
                    npm install netlify-cli node-jq
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=out --json > netlify-deploy.json
                '''
                script {
                    env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' netlify-deploy.json", returnStdout: true)
                }
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
                script {
                    def response = httpRequest(
                        url: env.STAGING_URL,
                        validResponseCodes: '100:599',
                        quiet: true,
                        consoleLogResponseBody: false
                    )

                    if (response.status != 200) {
                        error "Deployment failed with status code: ${response.status}"
                    }
                }
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