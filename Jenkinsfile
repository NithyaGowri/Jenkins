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
                    npm ci
                    npm run build
                '''
            }
        }
        stage('AWS Integraion'){
            agent
            {
                docker{
                    
                    image 'amazon/aws-cli'
                    args "--entrypoint=''"
                    reuseNode true
                }
            }
            environment
            {
                AWS_S3 = 's3-for-jenkins-inte'
            }
            steps{
                
                withCredentials([usernamePassword(credentialsId: 'awsaccesskey', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                    aws --version
                    #aws s3 ls > index.html
                    #aws s3 cp index.html s3://$AWS_S3/index.html
                    aws s3 sync build s3://$AWS_S3
                    '''
                }
                //sh "aws --version"
            }

        }
        
        
        
    }
    post
    {
        always
        {
            cleanWs()
        }
    }

    
}
