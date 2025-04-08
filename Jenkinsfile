pipeline {
    agent any

    tools {
        maven 'Maven'  // Define the tool name as configured in Jenkins
    }

    environment {
        SCANNER_HOME = tool 'my_sonar'  // Use the SonarQube tool
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', credentialsId: 'git_token', url: 'https://github.com/Hadakar-Kasturi/simple-java-project.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('my_sonar') {
                    sh """
                    mvn clean verify sonar:sonar \
                    -Dsonar.projectKey="demo-project-${env.BUILD_NUMBER}" \
                    -Dsonar.projectName="demo-Project - Build #${env.BUILD_NUMBER}" \
                    -Dsonar.qualitygate.wait=true \
                    -Dsonar.projectVersion=${BUILD_ID}
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                        // This step waits for the SonarQube quality gate status
                        def qualityGate = waitForQualityGate()
                        if (qualityGate.status != 'OK') {
                            error "Quality Gate failed! Aborting build."
                        }
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Artifact Upload') {
            steps {
                nexusArtifactUploader artifacts: [[
                    artifactId: 'my_repo_nexus', 
                    classifier: '', 
                    file: 'target/my_repo_nexus-1.0.war',  // Make sure this file exists
                    type: 'war'
                ]], 
                credentialsId: 'nexus', 
                groupId: 'works.buddy.samples', 
                nexusUrl: 'http://52.77.248.42:8081/', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'my_repo_nexus', 
                version: "${BUILD_ID}"
            }
        }
    }

    post {
        always {
            echo "Cleaning up after build..."
            cleanWs()  // Clean workspace after build to avoid issues in future runs
        }
        success {
            echo "Build and SonarQube analysis completed successfully!"
        }
        failure {
            echo "Build failed. Please check the logs for more details."
        }
    }
}
