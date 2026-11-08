pipeline {
    agent any

    stages {
        stage('Build1') {
            agent{
                docker{
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
        stage('Test1') {
            agent{
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
        }

         stage('E2E') {
            agent{
                docker {
                image ' mcr.microsoft.com/playwright:v1.64.1-noble'
                reuseNode true
                } 
                
            }
            steps {
                sh '''
                  npm install serve
                  node_modules/.bin/serve -s build 
                  npx playwright test
                '''
            }
        }
    }
    post{
        always{
            junit 'test-results/junit.xml'
        }
    }
}
