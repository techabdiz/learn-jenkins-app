pipeline {
    agent any
    stages {
        stage('Build') {
            agent docker {
                image 'node:18-alpine'
                reuseNode true
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
    }
}