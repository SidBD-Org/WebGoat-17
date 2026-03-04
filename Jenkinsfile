pipeline {
    agent any

    environment {
        POLARIS_SERVER_URL = "https://polaris.blackduck.com"
        POLARIS_ACCESS_TOKEN = credentials('prdPolarisTKN-Sid')
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Download Bridge CLI') {
            steps {
                sh '''
                    echo "Downloading Bridge CLI"

                    curl -L -o bridge-cli-bundle-linux64.zip https://repo.blackduck.com/bds-integrations-release/com/blackduck/integration/bridge/binaries/bridge-cli-bundle/latest/bridge-cli-bundle-linux64.zip

                    unzip -o bridge-cli-bundle-linux64.zip
                '''
            }
        }

        stage('Run Polaris SAST + SCA Scan') {
            steps {
                sh '''
                    echo "Running Polaris SAST + SCA"

                    chmod +x bridge-cli-bundle-linux64/bridge-cli

                    ./bridge-cli-bundle-linux64/bridge-cli \
                    --server-url=$POLARIS_SERVER_URL \
                    --access-token=$POLARIS_ACCESS_TOKEN \
                    --assessment-types=SAST,SCA \
                    --sca-scan-mode=signature,package
                '''
            }
        }

    }
}
