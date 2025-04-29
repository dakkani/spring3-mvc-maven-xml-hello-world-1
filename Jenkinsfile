pipeline {
    agent any

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "http://18.206.151.37:8081/"
        NEXUS_REPOSITORY = "simple-customer-app"
        NEXUS_CREDENTIAL_ID = "nexus-id"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo 'Checking out source code...'
                    git 'https://github.com/dakkani/spring3-mvc-maven-xml-hello-world-1.git'
                }
            }
        }

        stage('Build and Package') {
            steps {
                script {
                    echo 'Building and packaging the application...'
                    // Assuming 'mvn' is in your Jenkins agent's PATH
                    sh 'mvn -Dmaven.test.failure.ignore=true clean package'
                }
            }
        }

        stage('Publish to Nexus') {
            steps {
                script {
                    echo 'Publishing artifact to Nexus...'
                    def pom = readMavenPom file: 'pom.xml'
                    def artifactName = "${pom.artifactId}-${BUILD_NUMBER}.${pom.packaging}"
                    def artifactPath = "target/${artifactName}"

                    if (fileExists(artifactPath)) {
                        echo "Found artifact: ${artifactPath}"
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: "${BUILD_NUMBER}",
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [
                                    artifactId: pom.artifactId,
                                    classifier: '',
                                    file: artifactPath,
                                    type: pom.packaging
                                ],
                                [
                                    artifactId: pom.artifactId,
                                    classifier: '',
                                    file: 'pom.xml',
                                    type: 'pom'
                                ]
                            ]
                        )
                        echo "Successfully published ${artifactName} to Nexus: ${NEXUS_URL}${NEXUS_REPOSITORY}/${pom.groupId.replace('.', '/')}/${pom.artifactId}/${BUILD_NUMBER}/${artifactName}"
                    } else {
                        error "Artifact not found at: ${artifactPath}"
                    }
                }
            }
        }
    }
}
