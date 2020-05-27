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
    stage("Build Docker Image"){
      steps{
        script {
          app_image = docker.build("shauryapratap/udacity-capstone")
        }
      }
    }
    stage ('Deploy to EKS') {
      steps {
        sh "kubectl set image deployments/udacity-capstone udacity-capstone=shauryapratap/udacity-capstone:${env.GIT_COMMIT[0..7]} --record"
      }
    }
    stage("Cleaning up") {
      echo 'Cleaning up...'
      sh "docker system prune"
    }
}
