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

        stage ('All Tests') {
            parallel {
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
                            cd ..
                            rm -rf jest-results
                            mkdir jest-results
                            cp -r test-results/junit.xml jest-results/
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
                            npx playwright test --reporter=html 
                        '''
                    }
                }
            }
        }

        stage ('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to Netlify..."
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
           publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}