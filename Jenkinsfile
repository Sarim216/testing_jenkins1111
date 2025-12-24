pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Security Gate') {
            parallel {
                // SCAN 1: Semgrep (Code Logic & Secrets)
                stage('Semgrep SAST') {
                    steps {
                        echo '🕵️ Running Deep Code Analysis...'
                        // --config=p/default : Checks for standard coding bugs (Python, JS, Java, etc.)
                        // --config=p/secrets : Checks for hardcoded passwords, API keys, Tokens
                        // --error            : Fails the build immediately if issues are found
                        sh 'semgrep scan --config=p/default --config=p/secrets --error .'
                    }
                }
                
                // SCAN 2: Trivy (Library Vulnerabilities)
                stage('Trivy SCA') {
                    steps {
                        echo '📦 Scanning Dependencies (Libraries)...'
                        // fs .                     : Scans the FileSystem (current folder)
                        // --exit-code 1            : Fails the build if vulnerabilities are found
                        // --severity HIGH,CRITICAL : Only stops the build for major security risks
                        sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL .'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo '✅ Security Checks Passed. Deploying...'
                
                // DEBUG: Check permissions
                sh 'ls -la /home/prangan/Downloads/prod-server/'
                
                // FORCE COPY: Overwrite the live server file with the safe code
                sh 'cp -f index.html /home/prangan/Downloads/prod-server/index.html'
            }
        }
    }
}
