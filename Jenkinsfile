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
                    echo "Building the project..."
                    npm install
                    npm run build
                '''
            }
        }
    }
}