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
        stage('Deploying to EKS') {
            steps {
                dir('./') {
                    withAWS(credentials: 'aws-credentials', region: 'us-west-2') {
                            sh "aws eks --region us-west-2 update-kubeconfig --name EKSCluster-9Vdzvg9JaRtz"
                            sh 'kubectl apply -f app-deploy.yaml'
                        }
                    }
            }
        }
        }
    }

