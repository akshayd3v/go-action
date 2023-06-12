pipeline {
  agent any
  tools {
        dockerTool "docker"
    }
  stages {
    stage('Install SBOM tool') {
        steps {
            sh 'docker --version'
        }
    }
    stage('Build') {
        steps {
            sh 'docker run --rm -v /tmp:/tmp -v $(pwd):/app:rw -t ghcr.io/cyclonedx/cdxgen -r /app -o /app/bom.json'
        }
    }
}
}
