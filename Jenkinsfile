pipeline {
    agent any
    
    environment {
        APP_BUCKET = "app-code-bucket-eden-2026"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '‚úÖ Code checked out from Git'
                echo "Branch: ${env.GIT_BRANCH}"
                echo "Commit: ${env.GIT_COMMIT}"
            }
        }
        
        stage('Verify AWS CLI') {
            steps {
                bat 'aws --version'
            }
        }
        
        stage('Sync to S3') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', 
                                  credentialsId: 'aws-s3-credentials']]) {
                    bat """
                        echo Syncing to S3 bucket: %APP_BUCKET%
                        aws s3 sync . s3://%APP_BUCKET%/ ^
                            --delete ^
                            --exclude ".git/*" ^
                            --exclude ".gitignore" ^
                            --exclude "*.tfstate*" ^
                            --exclude "*.terraform/*"
                    """
                }
            }
        }
        
        stage('Verify Upload') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', 
                                  credentialsId: 'aws-s3-credentials']]) {
                    bat """
                        echo Files in S3:
                        aws s3 ls s3://%APP_BUCKET%/ --recursive
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo 'Ìæâ Files successfully synced to S3!'
            echo "Check S3: https://s3.console.aws.amazon.com/s3/buckets/${APP_BUCKET}"
        }
        failure {
            echo '‚ùå Failed to sync files to S3'
        }
        always {
            echo "Build completed at: ${new Date()}"
        }
    }
}
