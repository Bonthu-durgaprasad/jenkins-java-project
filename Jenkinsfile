pipeline {
    agent any

    environment {
        AWS_CREDENTIALS = credentials('cred') // Use the same credentials ID
        S3_BUCKET = 'my-codedeploy-bucket-100' 
        CODEDEPLOY_APP_NAME = 'cicd-code-deploy'
        DEPLOYMENT_GROUP = 'deployment-group-cicd'
        REGION = 'us-east-1'
    }

    stages {
        stage ('checkout') {
            steps {
                script {
                    git 'https://github.com/Bonthu-durgaprasad/jenkins-java-project.git'
                }
            }
        }
        stage ('build') {
            steps {
                script {
                    sh "mvn clean package"
                }
            }
        }
        stage ('package') {
            steps {
                script {
                    echo 'Packaging the application...'
                     sh 'zip -r app.zip target/*'
                }
            }
        }
        
        
        
        
        stage('Package and Upload to S3') {
            steps {
                script {
                    echo 'Packaging application and uploading to S3...'
                    sh 'zip -r app.zip *'

                    // Use AWS credentials defined once for all AWS commands
                    sh """
                    aws s3 cp app.zip s3://${S3_BUCKET}/app.zip \
                    --region ${REGION} \
                    --access-key-id ${AWS_CREDENTIALS_USR} \
                    --secret-access-key ${AWS_CREDENTIALS_PSW}
                    """
                }
            }
        }

        stage('Deploy to AWS CodeDeploy') {
            steps {
                script {
                    echo 'Deploying to AWS CodeDeploy...'
                    sh """
                    aws deploy create-deployment \
                    --application-name ${CODEDEPLOY_APP_NAME} \
                    --deployment-group-name ${DEPLOYMENT_GROUP} \
                    --deployment-config-name CodeDeployDefault.OneAtATime \
                    --s3-location bucket=${S3_BUCKET},bundleType=zip,key=app.zip \
                    --region ${REGION} \
                    --access-key-id ${AWS_CREDENTIALS_USR} \
                    --secret-access-key ${AWS_CREDENTIALS_PSW}
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed. Please check the logs.'
        }
    }
}
