pipeline {
  agent {
    // Use the Jenkins Docker image as the agent
    docker {
      image 'jenkins/jenkins:latest'
      args '-v /var/run/docker.sock:/var/run/docker.sock'
    }
  }
  
  stages {
    stage('Build') {
      steps {
        sh 'docker run --rm -v /tmp:/tmp -v $(pwd):/app:rw -t ghcr.io/cyclonedx/cdxgen -r /app -o /app/bom.json'
      }
    }
  }
}
