pipeline{
    agent any

    stages{
        stage("Build Docker Image"){
            steps{
                script {
		    sh 'sudo groupadd docker'
                    sh 'sudo usermod -aG docker ${USER}'
                    sh 'su -s ${USER}'
                    sh 'sudo chown "$USER":"$USER" /home/"$USER"/.docker -R'
                    sh 'sudo chmod g+rwx "$HOME/.docker" -R'
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
