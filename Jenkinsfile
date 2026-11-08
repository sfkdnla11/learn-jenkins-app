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
    }
    post{
        always{
            junit 'test-results/junit.xml'
        }
    }
}
