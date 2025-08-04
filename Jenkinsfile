pipeline {
    agent any
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    echo "checking node and npm versions..."
                    node --version
                    npm --version


                    echo "Building the project..."
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f build/index.html || (echo "Build failed, index.html not found!" && exit 1)
                    cd build
                    npm test
                    mkdir jest-results
                    cp -r jest/junit.xml jest-results/
                    echo "Tests completed successfully, results stored in jest-results directory."
                    ls -lrt jest-results
                '''
            }
        }

        stage('E2E Tests') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Running E2E tests..."
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npm playwright test
                '''
            }
        }
    }

    post {
        always {
        sh '''
            ls -lrt jest-results
        '''
           junit 'jest-results/junit.xml'
        }
    }
}