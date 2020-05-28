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
                sh "kubectl set image deployments/udacity-capstone udacity-capstone=shauryapratap/udacity-capstone:${env.GIT_COMMIT[0..7]} --record"
            }
        }
        }
    }

