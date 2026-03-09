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
                curl -L -o bridge.zip https://repo.blackduck.com/bds-integrations-release/com/blackduck/integration/bridge-cli-bundle-linux64.zip
                unzip bridge.zip
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
                polaris.application.name=WebGoat-App \
                polaris.project.name=WebGoat-17 \
                polaris.branch.name=jenkins-scan \
                polaris.assessment.types=SAST,SCA \
                polaris.test.sca.type=SCA-PACKAGE,SCA-SIGNATURE \
                coverity.build.command="./mvnw clean install -DskipTests"
            '''
        }
    }

}
