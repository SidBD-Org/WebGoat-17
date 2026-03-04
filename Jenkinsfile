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
                withCredentials([string(credentialsId: 'prdPolarisTKN-Sid', variable: 'POLARIS_ACCESS_TOKEN')])
                sh '''
                    echo "Running Polaris SAST + SCA"

                    chmod +x bridge-cli-bundle-linux64/bridge-cli

                    export POLARIS_SERVER_URL=https://polaris.blackduck.com
                    export POLARIS_ACCESS_TOKEN=$POLARIS_ACCESS_TOKEN

                    ./bridge-cli-bundle-linux64/bridge-cli \
                    --assessment-types=SAST,SCA \
                    --sca-scan-mode=signature,package
                '''
            }
        }

    }
}
