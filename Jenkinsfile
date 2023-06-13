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

pipeline {
    agent any
       
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('DT-check') {
            steps {
                dependencyCheck additionalArguments: '--format HTML', odcInstallation: 'dependency-check'
            }
        }
    }
}



