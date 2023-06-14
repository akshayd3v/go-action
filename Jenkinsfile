pipeline {
    agent any

    tools {
        dockerTool 'docker'
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

        stage('Download sbom-tool') {
            steps {
                sh 'curl -Lo sbom-tool https://github.com/microsoft/sbom-tool/releases/latest/download/sbom-tool-linux-x64'
                sh 'chmod +x sbom-tool'
            }
        }

        stage('Generate SBOM') {
            steps {
                sh './sbom-tool generate -o bom.json'
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
