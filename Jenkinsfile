pipeline {
    agent any

    tools {
        maven 'maven'  // Ensure the correct Maven tool is configured
    }

    environment {
        // SCANNER_HOME is not needed unless you're explicitly pointing to a specific path for the scanner
        // SCANNER_HOME = tool 'my_sonar'  // Path to SonarQube tool (not needed if using withSonarQubeEnv)
    }

    stages {
        // Stage to checkout the code from Git
        stage('Checkout') {
            steps {
                git branch: 'master', credentialsId: 'git_token', url: 'https://github.com/Hadakar-Kasturi/simple-java-project.git'
            }
        }

        // Stage for SonarQube analysis
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('my_sonar') {
                    sh """
                    mvn clean install sonar:sonar \
                    -Dsonar.projectKey="Test-project-${env.BUILD_NUMBER}" \
                    -Dsonar.projectName="Test-Project - Build #${env.BUILD_NUMBER}" \
                    -Dsonar.qualitygate.wait=true \
                    -Dsonar.projectVersion=${BUILD_ID}
                    """
                }
            }
        }

        // Stage to wait for and check the SonarQube Quality Gate status
        stage('Quality Gate') {
            steps {
                script {
                    // Set a timeout to wait for the quality gate result
                    timeout(time: 5, unit: 'MINUTES') {
                        def qualityGate = waitForQualityGate()  // Get quality gate result
                        if (qualityGate.status != 'OK') {  // Check if the quality gate status is not OK
                            error "Quality Gate failed! Aborting pipeline."  // Abort pipeline if it fails
                        }
                    }
                }
            }
        }
    }

    // Post actions: cleanup or notifications
    post {
        always {
            echo "Pipeline completed"
            cleanWs()  // Clean workspace after the build
        }
        success {
            echo "Build and SonarQube analysis completed successfully!"
        }
        failure {
            echo "Build failed. Please check the logs for more details."
        }
    }
}
