pipeline {
    agent any

   stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
       
        
        stage('Generate SBOM') {
            steps {
                // Install CDXGEN tool
                sh 'npm install -g @cyclonedx/bom'
                
                // Generate SBOM using CDXGEN
                sh 'cd go-action && cyclonedx-bom generate --input go.mod --output sbom.xml'
            }
        }
        
        stage('Publish SBOM') {
            steps {
                // Publish the generated SBOM as an artifact
                archiveArtifacts artifacts: 'your-go-repo/sbom.xml', fingerprint: true
                
                // Optionally, you can also publish the SBOM to a specific location or service
                // e.g., upload it to a file server, publish it to a vulnerability management tool, etc.
            }
        }
    }
}
