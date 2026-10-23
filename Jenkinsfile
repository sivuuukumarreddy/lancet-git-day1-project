pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {
        stage('Inspect Repository') {
            steps {
                echo 'Inspecting the checked-out repository'
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Validate Source') {
            steps {
                sh '''
                    set -eu
                    test -f Jenkinsfile
                    echo "Source validation passed"
                '''
            }
        }

        stage('Build Artifact') {
            steps {
                sh '''
                    set -eu
                    mkdir -p output
                    printf 'Jenkins CI build %s\n' "$BUILD_NUMBER" \
                      > output/build-info.txt

                    if [ -f README.md ]; then
                        cp README.md output/README.md
                    fi

                    echo "Build artifact created"
                    ls -l output
                '''
            }
        }

        stage('Test Artifact') {
            steps {
                sh '''
                    set -eu
                    test -s output/build-info.txt
                    grep -q "Jenkins CI build" output/build-info.txt
                    echo "Artifact test passed"
                '''
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts(
                    artifacts: 'output/**',
                    fingerprint: true
                )
            }
        }
    }

    post {
        success {
            echo 'CI pipeline completed successfully'
        }

        failure {
            echo 'CI pipeline failed. Check the stage logs.'
        }

        always {
            echo 'Pipeline execution has ended'
        }
    }
}
