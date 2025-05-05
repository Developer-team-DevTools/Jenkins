pipeline {
    agent any

    environment {
        OCTOPUS_API_KEY = credentials('octopus-api-key')       // ID from Jenkins Credentials
        OCTOPUS_URL = 'https://devtools.octopus.app/'           // Your Octopus URL
        SPACE = 'Spaces-122'                                   // Your Octopus Space ID
        PACKAGE_PATH = "target/${ARTIFACT_NAME}"                // Package path with dynamic name
        PACKAGE_VERSION = "1.0.${BUILD_NUMBER}"                 // Dynamic version: 1.0.1, 1.0.2, etc.
    }

    stages {
        stage('Build Project') {
            steps {
                // Build the project with dynamic version and final name
                sh "mvn clean package -Djar.finalName=hello-world.${PACKAGE_VERSION}"
                // List the files in the target directory to verify the .jar file exists
                sh 'ls target/'
            }
        }

        stage('Archive JAR') {
            steps {
                // Archive the correctly named JAR for record-keeping in Jenkins
                archiveArtifacts artifacts: "target/hello-world.${PACKAGE_VERSION}.jar", fingerprint: true
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
                    --package "target/hello-world.${PACKAGE_VERSION}.jar" \
                    --space "${SPACE}" \
                    --overwrite-mode overwrite
                '''
            }
        }
    }
}
