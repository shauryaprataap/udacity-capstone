pipeline{
    agent any

    stages{
        stage("Linting"){
            steps{
                echo "Linting HTML Code"
                sh "tidy --drop-empty-elements false --drop-empty-paras false -qe public/*.html"
                echo "Linting Dockerfile"
                sh "hadolint Dockerfile"
            }
        }
        stage("Build Docker Image"){
            steps{
                script {
                    app_image = docker.build("shauryapratap/udacity-capstone")
                }
            }
        }
        stage ('Push Image to Registry') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        app_image.push("${env.GIT_COMMIT[0..7]}")
                        app_image.push("latest")
                    }
                }
            }
        }
        stage ('Deploy to EKS') {
            steps {
                  withAWS(region:'us-west-2',credentials:'aws-credentials') {
                    sh "aws eks --region eu-central-1 update-kubeconfig --name CapstoneEKS-VUUZkwHTDVPa"
                    sh "kubectl apply -f aws/aws-auth-cm.yaml"
                    sh "kubectl set image deployments/capstone-app capstone-app=${registry}:latest"
                    sh "kubectl apply -f app-deploy.yaml"
		    sh "kubectl apply -f app-service.yaml"
                    sh "kubectl get nodes"
                    sh "kubectl get pods"
                    sh "aws cloudformation update-stack --stack-name udacity-workers --template-body  --parameters udacity-workers-params.json --capabilities CAPABILITY_IAM"
                  }
              }
        }
    }
}
