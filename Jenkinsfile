node {
    def registry = 'shauryapratap/udacity-capstone'
    stage('Checking out git repo') {
      echo 'Checkout...'
      checkout scm
    }
    stage('Checking environment') {
      echo 'Checking environment...'
      sh 'git --version'
      echo "Branch: ${env.BRANCH_NAME}"
      sh 'docker -v'
    }
    stage('Deploying') {
      echo 'Deploying to AWS...'
      dir ('./') {
        withAWS(credentials: 'aws-credentials', region: 'eu-central-1') {
            sh "aws eks --region us-west-2 update-kubeconfig --name EKSCluster-9Vdzvg9JaRtz"
            sh "kubectl apply -f aws/aws-auth-cm.yaml"
            sh "kubectl set image deployments/capstone-app capstone-app=${registry}:latest"
            sh "kubectl apply -f app-deploy.yml"
            sh "kubectl get nodes"
            sh "kubectl get pods"
            sh "aws cloudformation update-stack --stack-name udacity-capstone-nodes --template-body file://udacity-workers.yaml --parameters file://udacity-workers-params.json --capabilities CAPABILITY_IAM"
        }
      }
    }
    stage("Cleaning up") {
      echo 'Cleaning up...'
      sh "docker system prune"
    }
}
