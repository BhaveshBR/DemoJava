pipeline {
    agent any 
    environment { 
        AWS_ACCOUNT_ID="617292774228"
        AWS_DEFAULT_REGION="us-east-2"
        IMAGE_REPO_NAME="demojava"
        IMAGE_TAG="latest"
        EKS_CLUSTER_NAME = "simplilearn-eks"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
        NAMESPACE = "dev"
    }
    stages {
        stage('Logging into AWS ECR and AWS EKS') {
            steps {
                sh '''
                  echo "Logging into AWS ECR and AWS EKS"
                  aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com
                  aws eks update-kubeconfig --region ${AWS_DEFAULT_REGION} --name ${EKS_CLUSTER_NAME}
                '''
            }
        }
    }
    stages {
        stage('Build Docker Image') {
            steps {
                sh '''
                  echo "Build Docker Image"
                  mvn clean
                  mvn install
                  docker build -t  ${IMAGE_REPO_NAME}:${IMAGE_TAG} .
                '''
            }
        }
    }
    stages {
        stage('Push Docker Image to ECR') {
            steps {
                sh '''
                  echo "Push Docker Image to ECR"
                  docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                  docker push ${REPOSITORY_URI}:${IMAGE_TAG}"
                '''
            }
        }
    }
    stages {
        stage('Deploy to Image to EKS') {
            steps {
                sh '''
                  echo "Deploy to Image to EKS"
                  git clone https://github.com/BhaveshBR/DemoHelm.git
                  cd DemoHelm
                  git pull
                  helm upgrade sh-insights-{NAMESPACE} sh-insights --set image=${REPOSITORY_URI}:${IMAGE_TAG} --namespace ${NAMESPACE}
                '''
            }
        }
    }
}
