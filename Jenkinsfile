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
                    echo "Poll SCM Trigger"
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Seeking Approval') {
            steps {
                input cancel: 'No, Abort', message: 'Are you Sure to Proceed Testing Stage', ok: 'Yes, Proceed with Testing'
            }
        }

        stage('Tests')
        {
            parallel{
                stage('Unit Test') {
                agent {
                    docker {
                        image 'node:18-alpine'
                        reuseNode true
                    }
                }

                steps {
                    sh '''
                        test -f build/index.html
                        npm test
                    '''
                }
            }
            stage('E2E Testing') {
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
                        npx playwright test
                    '''
                }
            }


            }
        }
        
    }

    post {
        always {
            junit 'e2e-results/junit.xml'
        }
    }
}
