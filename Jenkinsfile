pipeline {
    agent any

    stages {
        stage('AWS Integraion'){
            agent
            {
                docker{
                    image 'amazon/aws-cli'
                    args "--entrypoint=''"
                }
            }
            steps{
                sh 'aws --version"
            }

        }
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
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
        
        stage('Tests')
        {
            
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
        }
        
    }

    post {
        always {
            junit 'e2e-results/junit.xml'
        }
    }
}
