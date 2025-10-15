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
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'Test stage'
                sh '''
                    # Check if index.html exists inside the build directory
                    if [ -f build/index.html ]; then
                        echo "✅ index.html exists inside the build directory."
                    else
                        echo "❌ index.html does NOT exist inside the build directory."
                        exit 1
                    fi

                    echo "✅ Running unit tests..."
                    npm test
                '''
            }
        }

        
        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.56.0-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                   npm install -g serve
                   ./node_modules/.bin/serve -s build &
                   sleep 10
                   npx playwright test
                '''
            }
        }
    }

    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}