pipeline {
    agent any
    
      tools {
         dockerTool "docker"
     }   

    stages {
        stage('Check Docker Version') {
            steps {
                sh 'docker --version'
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }
         stage('Start and Enable Docker') {
            steps {
                sh 'sudo systemctl start docker'
                sh 'sudo systemctl enable docker'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    try {
                        sh 'docker pull ghcr.io/cyclonedx/cdxgen:v8.5.3'
                    } catch (Exception e) {
                        error("Failed to pull the Docker image: ${e.getMessage()}")
                    }
                }
            }
        }

        stage('Generate SBOM') {
            steps {
                sh 'docker run --rm -v $(pwd):/app -w /app ghcr.io/cyclonedx/cdxgen:v8.5.3 -r . -o bom.json'
                script {
                    def sbom = readFile('bom.json')
                    echo "Generated SBOM:\n$sbom"
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
}
