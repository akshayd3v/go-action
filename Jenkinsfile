// pipeline {
//     agent any
//     tools {
//         dockerTool "docker"
//     }    

//     stages {
//         stage('Build') {
//             steps {
//                 script {
//                     docker.image('ghcr.io/cyclonedx/cdxgen').inside {
//                         // Docker run command
//                         sh 'docker run --rm -v /tmp:/tmp -v $(pwd):/app:rw -t ghcr.io/cyclonedx/cdxgen -r /app -o /app/bom.json'
//                     }
//                 }
//             }
//         }
//     }
// }
// pipeline {
//     agent any
//     tools {
//         dockerTool "docker"
//     }    

//     stages {
//         stage('Setup') {
//             steps {
//                 // Check Docker version
//                 sh 'docker --version'

//                 // Start Docker daemon
//                 script {
//                     sh 'su -c "service docker start"'  // Adjust this command based on your OS
//                     sh 'systemctl status docker'  // Optional: Verify Docker daemon status
//                 }
//             }
//         }
        
//         stage('Build') {
//             steps {
//                 script {
//                     docker.image('ghcr.io/cyclonedx/cdxgen').inside {
//                         // Docker run command
//                         sh 'docker run --rm -v /tmp:/tmp -v $(pwd):/app:rw -t ghcr.io/cyclonedx/cdxgen -r /app -o /app/bom.json'
//                     }
//                 }
//             }
//         }
//     }
// }
pipeline{
    agent any
    stages{
        stage("Build Jar"){
          sh 'mvn clean package'
        }
        stage("Generate SBOM with CycloneDX maven  plugin"){
          sh "mvn org.cyclonedx:cyclonedx-maven-plugin:makeBom"
         
        }
        stage('Upload SBOM to Dependency-Track') {
          sh """
          curl -k -X POST "https://dt-api-jenkins-test.staging.cryptosoft.com/api/v1/bom" \
          -H "Content-Type:multipart/form-data" \
          -H "X-Api-Key:sdIH6vUsngEE9ENDRygz805LRDrqiB3H" \
          -F "autoCreate=true" \
          -F "projectName=testJenkins" \
          -F "projectVersion=1.24" \
          -F "bom=@bom.json"
          """
            }
            }
        }




