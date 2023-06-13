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

        stage('Download cdxgen Binary') {
            steps {
                sh '''
                wget https://github.com/CycloneDX/cdxgen/releases/download/v8.5.3/cdxgen_8.5.3_linux_amd64.tar.gz
                tar -xf cdxgen_8.5.3_linux_amd64.tar.gz
                chmod +x cdxgen
                '''
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh './cdxgen -r . -o bom.json'
            }
        }

        stage('Generate SBOM') {
            steps {
                sh 'export FETCH_LICENSE=true && ./cdxgen -o bom.json'
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
