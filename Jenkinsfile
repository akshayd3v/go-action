pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install syft') {
            steps {
                sh 'curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b $HOME/bin v0.83.0'
            }
        }

        stage('Generate SBOM') {
            steps {
                sh '$HOME/bin/syft version'
                sh '$HOME/bin/syft packages alpine:latest -o cyclonedx.json'
                script {
                    def sbom = readFile('cyclonedx.json')
                    echo "Generated CycloneDX SBOM:\n$sbom"
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
                    -F "bom=@cyclonedx.json"
                    """
                }
            }
        }
    }
}
