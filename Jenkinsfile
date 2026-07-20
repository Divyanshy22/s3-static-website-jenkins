pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:/usr/local/bin:${env.PATH}"
        BUCKET_NAME = 'static-website-s3'
        AWS_REGION = 'us-east-1'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to S3') {
            steps {
                withCredentials([
                    string(credentialsId: 'access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                        aws s3 sync . s3://$BUCKET_NAME \
                          --exclude ".git/*" \
                          --exclude "Jenkinsfile" \
                          --region $AWS_REGION
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Site deployed successfully to http://${env.BUCKET_NAME}.s3-website-${env.AWS_REGION}.amazonaws.com"
        }
        failure {
            echo 'Deployment failed — check console output above.'
        }
    }
}
