pipeline {
    agent any
    environment {
        AWS_REGION = 'us-east-1'
        ACCOUNT_ID = '222348769973'
        ECR_REPO   = 'nginx-app'
        IMAGE_URI  = "${222348769973}.dkr.ecr.${us-east-1}.amazonaws.com/${nginx-app}"
        CLUSTER    = 'nginx-cluster'
        SERVICE    = 'nginx-service'
    }
    stages {
        stage('Checkout') { steps { checkout scm } }
        stage('Build')    { steps { sh "docker build -t ${ECR_REPO}:${BUILD_NUMBER} ." } }
        stage('Push to ECR') {
            steps {
                sh '''
                    aws ecr get-login-password --region ${AWS_REGION} \
                      | docker login --username AWS --password-stdin ${IMAGE_URI}
                    docker tag ${ECR_REPO}:${BUILD_NUMBER} ${IMAGE_URI}:latest
                    docker push ${IMAGE_URI}:latest
                '''
            }
        }
        stage('Deploy to ECS') {
            steps {
                sh 'aws ecs update-service --cluster ${CLUSTER} --service ${SERVICE} --force-new-deployment --region ${AWS_REGION}'
            }
        }
    }
    post { always { sh 'docker image prune -f || true' } }
}
