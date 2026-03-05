pipeline {
  agent any

  tools {
    jdk 'openjdk-17'        // adjust to your Jenkins JDK tool name if different
    maven 'maven-3'         // adjust to your Jenkins Maven tool name if different
  }

  environment {
    POLARIS_SERVER_URL     = 'https://polaris.blackduck.com'
    POLARIS_ACCESS_TOKEN   = credentials('prdPolarisTKN-Sid')  // Jenkins Secret Text
    BRIDGE_BUNDLE_URL      = 'https://repo.blackduck.com/artifactory/bds-integrations-release/com/blackduck/integration/bridge/binaries/bridge-cli-bundle/latest/bridge-cli-bundle-linux64.zip'

    // From your Polaris structure (screenshot)
    POLARIS_APPLICATION    = 'WebGoatSid-Jenkins'
    POLARIS_PROJECT        = 'WebGoatSid-Jenkins'
    POLARIS_BRANCH         = 'jenkinsTest-17'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build (for SAST capture)') {
      steps {
        sh 'mvn -B clean install -DskipTests'
      }
    }

    stage('Download Bridge CLI') {
      steps {
        sh '''
          echo "Downloading Bridge CLI bundle..."
          curl -fLsS -o bridge.zip "$BRIDGE_BUNDLE_URL"
          unzip -qo bridge.zip
          rm -f bridge.zip

          # Current bundles extract 'bridge-cli' at repo root (no nested path)
          chmod +x bridge-cli
          ./bridge-cli --version || true
        '''
      }
    }

    stage('Polaris SAST + SCA') {
      steps {
        sh '''
          echo "Running Polaris SAST + SCA..."
          ./bridge-cli \
            --stage polaris \
            polaris.serverUrl="$POLARIS_SERVER_URL" \
            polaris.accessToken="$POLARIS_ACCESS_TOKEN" \
            polaris.application.name="$POLARIS_APPLICATION" \
            polaris.project.name="$POLARIS_PROJECT" \
            polaris.branch.name="$POLARIS_BRANCH" \
            polaris.assessment.types=SAST,SCA \
            polaris.test.sca.type=SCA-SIGNATURE,SCA-PACKAGE \
            polaris.reports.sarif.create=true
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts allowEmptyArchive: true, artifacts: '.bridge/**'
      cleanWs()
    }
  }
}
