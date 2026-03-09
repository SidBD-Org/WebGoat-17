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
                rm -rf bridge-cli-bundle-linux64
                curl -L -o bridge.zip https://repo.blackduck.com/bds-integrations-release/com/blackduck/integration/bridge/binaries/bridge-cli-bundle/latest/bridge-cli-bundle-linux64.zip
                unzip -o bridge.zip
                chmod +x bridge-cli-bundle-linux64/bridge-cli
                '''
            }
        }

        stage('Run Polaris Scan') {
            steps {
                sh '''
                    ./bridge-cli-bundle-linux64/bridge-cli \
                    --stage polaris \
                    polaris.serverUrl=https://polaris.blackduck.com \
                    polaris.accessToken=$POLARIS_TOKEN \
                    polaris.application.name=Test \
                    polaris.project.name=WGsid-jenkinsPipeline-17 \
                    polaris.branch.name=jenkinsPipeline-17 \
                    polaris.assessment.types=SAST,SCA \
                    polaris.test.sca.type=SCA-PACKAGE,SCA-SIGNATURE \
                    coverity.build.command="./mvnw clean install -DskipTests"
                '''
            }
        }

    }
}
