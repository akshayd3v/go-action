pipeline {
    agent any

    tools {
        go 'go'
        nodejs 'node'
    }
  

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'go mod why'
            }
        }
        
        stage('Install SBOM tool') {
            steps {
                sh 'npm install -g @cyclonedx/cdxgen'
            }
        }
        
        stage('Generate SBOM') {
            steps {
                sh 'export FETCH_LICENSE=true && cdxgen -r -o bom.json'
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
