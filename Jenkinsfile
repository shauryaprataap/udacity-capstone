pipeline{
    agent any

    stages{
        stage("Build Docker Image"){
            steps{
                script {
		    sh "chmod 777 /var/run/docker.sock"
                    app_image = docker.build("shauryapratap/udacity-capstone")
                }
            }
        }
        stage ('Push Image to Registry') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_id') {
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
