pipeline {
    agent any

    environment {
        OCTOPUS_API_KEY = credentials('octopus-api-key')       // ID from Jenkins Credentials
        OCTOPUS_URL = 'https://devtools.octopus.app/'           // Your Octopus URL
        SPACE = 'Spaces-122'                                   // Your Octopus Space ID
        ARTIFACT_NAME = "helloworld_${BUILD_NUMBER}.jar"       // Artifact name with build number
        PACKAGE_PATH = "target/${ARTIFACT_NAME}"               // Package path with dynamic name
    }

    stages {
        stage('Build with Maven') {
            steps {
                // Build the project and set the finalName dynamically
                sh "mvn clean package -Djar.finalName=helloworld_${BUILD_NUMBER}"
            }
        }

        stage('Login to Octopus CLI') {
            steps {
                sh '''
                    octopus login \
                      --server "${OCTOPUS_URL}" \
                      --api-key "${OCTOPUS_API_KEY}"
                '''
            }
        }

        stage('Upload to Octopus Deploy') {
            steps {
                // Upload the artifact to Octopus Deploy
                sh '''
                  octopus package upload \
                    --package "target/helloworld-${BUILD_NUMBER}.jar" \
                    --space "${SPACE}" \
                    --overwrite-mode overwrite
                '''
            }
        }
    }
}
