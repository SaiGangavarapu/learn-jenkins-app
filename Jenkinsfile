pipeline {
    agent any

    stages {
        stage('AWS'){
            agent{
                docker{
                    image 'amazon/aws-cli'
                    args '--entrypoint=""'
                }
            }
            environment{
                AWS_S3_BUCKET = 'jenkinsproject'
            }
            steps{
                withCredentials([usernamePassword(credentialsId: 'AWS-ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh'''
                    aws --version
                    echo 'Hello welcome to S3!' > index.html
                    aws s3 cp index.html s3://$AWS_S3_BUCKET/index.html
                    '''
                }
                
            }
        }
        stage('Build') {
            agent{
                docker{
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
        
        stage('Test'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh'''
                    test -f  build/index.html
                    npm test
                '''
            }
        }
    }
}
