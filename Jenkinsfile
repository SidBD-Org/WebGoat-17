pipeline {
    agent any

    tools {
        jdk 'openjdk-17'
    }

    environment {
        POLARIS_TOKEN = credentials('prdPolarisTKN-Sid')
    }

    stages {

        stage('Verify Java') {
            steps {
                sh '''
                java -version
                echo $JAVA_HOME
                '''
            }
        }

        stage('Build Application') {
            steps {
                sh './mvnw clean install -DskipTests'
            }
        }

        stage('Download Polaris Bridge CLI') {
            steps {
                sh '''
                curl -L -o bridge.zip https://repo.blackduck.com/bds-integrations-release/com/blackduck/integration/bridge/binaries/bridge-cli-bundle/latest/bridge-cli-bundle-linux64.zip
                unzip bridge.zip
                chmod +x bridge-cli-bundle-linux64/bridge-cli
                '''
            }
        }
    }
}
