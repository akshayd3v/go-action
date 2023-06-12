pipeline {
    agent any
    tools {
        dockerTool "docker"
    }    

    stages {
        stage('Build') {
            steps {
                script {
                    docker.image('ghcr.io/cyclonedx/cdxgen').inside {
                        // Docker run command
                        sh 'docker run --rm -v /tmp:/tmp -v $(pwd):/app:rw -t ghcr.io/cyclonedx/cdxgen -r /app -o /app/bom.json'
                    }
                }
            }
        }
    }
}
