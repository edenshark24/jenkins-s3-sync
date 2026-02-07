pipeline {
    agent any
    
    environment {
        S3_BUCKET = "tf-state-bucket-eden-2026"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Code checked out from Git'
            }
        }
        
        stage('Sync to S3') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', 
                                  credentialsId: 'aws-s3-credentials']]) {
                    bat """
                        aws s3 sync . s3://%S3_BUCKET%/ --delete --exclude ".git/*"
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Files successfully synced to S3!'
        }
        failure {
            echo '❌ Failed to sync files to S3'
        }
    }
}
