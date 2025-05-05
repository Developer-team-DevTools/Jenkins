pipeline {
    agent any

    environment {
        OCTOPUS_API_KEY = credentials('octopus-api-key')       // ID from Jenkins Credentials
        OCTOPUS_HOST = 'https://devtools.octopus.app/'         // Your Octopus URL
        SPACE = 'Spaces-122'                                   // Your Octopus Space ID
        ARTIFACT_NAME = "hello-world_${BUILD_NUMBER}.jar"      // Artifact name with build number
        PACKAGE_PATH = "target/${ARTIFACT_NAME}"               // Package path with dynamic name
    }

    stages {
        stage('Build with Maven') {
            steps {
                sh "mvn clean package -Djar.finalName=${ARTIFACT_NAME}"  // Use dynamic name for the artifact
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: "target/${ARTIFACT_NAME}", fingerprint: true  // Archive the generated jar
            }
        }

        stage('Upload to Octopus Deploy') {
            steps {
                sh '''
                  octopus package upload \
                    --package "target/${ARTIFACT_NAME}" \
                    --space "${SPACE}" \
                    --server "${OCTOPUS_HOST}" \
                    --api-key "${OCTOPUS_API_KEY}" \
                    --overwrite-mode overwrite
                '''
            }
        }
    }
}
