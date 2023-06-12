pipeline {
    agent any

    tools {
        dockerTool "docker"
    }
    
    stages {
        stage('Build') {
        steps {
            sh 'docker run --rm -v /tmp:/tmp -v $(pwd):/app:rw -t ghcr.io/cyclonedx/cdxgen -r /app -o /app/bom.json'
        }
        }
    }
    

              
    stage('Upload SBOM to Dependency-Track') {
        steps {
            withCredentials([string(credentialsId: 'apikey', variable: 'X_API_KEY')]) {
                sh """
                curl -k -X POST "https://dt-api-jenkins-test.staging.cryptosoft.com/api/v1/bom" \
                -H "Content-Type:multipart/form-data" \
                -H "X-Api-Key:${X_API_KEY}" \
                -F "autoCreate=true" \
                -F "projectName=testJenkins" \
                -F "projectVersion=1.24" \
                -F "bom=@bom.json"
                """
                }
            }
        }
    }

