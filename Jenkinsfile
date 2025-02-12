pipeline {
    agent any
    
    environment {
        NETLIFY_SITE_ID = 'ab98e398-85d4-4746-a147-2d32364780fb'
        NETLIFY_AUTH_TOKEN = credentials('netlifyak')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'mynodejs:latest'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm ci
                    npm run build
                '''
            }
        }
        stage('Tests') {
            parallel {
                stage('Running tests') {
                    agent {
                        docker {
                            image 'mynodejs:latest'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm run test
                        '''
                    }
                }
                stage('Reporting') {
                    agent {
                        docker {
                            image 'mynodejs:latest'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm run test-coverage
                        '''
                    }
                }
            }
        }
        stage('Deploiement') {
            agent {
                docker {
                    image 'mynodejs:latest'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    netlify deploy --dir=out --prod
                '''
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'test-report.html'
        }
    }
}