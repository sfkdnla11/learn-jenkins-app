pipeline {
    agent any

    stages {
        stage('Build1') {
            agent {
                docker {
                    image 'node:22-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                   mkdir -p build
                   ls -la
                   node --version
                   npm --version
                   npm ci
                   npm run build
                '''
            }
        }

        stage('Tests') {
            parallel {
                stage('Test1') {
                    agent {
                        docker {
                            image 'node:22-alpine'
                            reuseNode true
                        } 
                    }
                    steps {
                        sh '''
                           npm run test
                           test -f build/index.html
                        '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }

                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        } 
                    }
                    steps {
                        sh '''
                          npm install serve
                          node_modules/.bin/serve -s build &
                          sleep 10
                          npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }
        
        stage('Deploy') {
            agent {
                docker {
                    image 'node:22-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                   npm install @playwright/cli@latest
                   playwright-cli --help
                   node_modules/.bin/netlify --version
                '''
            }
        }
    }
}