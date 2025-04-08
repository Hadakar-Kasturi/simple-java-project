pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SCANNER_HOME = tool 'my_sonar'  
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', credentialsId: 'git_token', url: 'https://github.com/Hadakar-Kasturi/simple-java-project.git'
            }
        }

        stage('SonarQube') {
            steps {
                withSonarQubeEnv('my_sonar') { 
                    sh """
                    mvn sonar:sonar \
                    -Dsonar.projectKey="demo-project-${env.BUILD_NUMBER}" \
                    -Dsonar.projectName="demo-Project - Build #${env.BUILD_NUMBER}" \
                    -Dsonar.qualitygate.wait=true \
                    -Dsonar.projectVersion=${BUILD_ID} \
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: false
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Artifact Upload') {
            steps {
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'my_repo_nexus',
                        classifier: '',
                        file: 'target/my_repo_nexus-1.0.war', // Corrected file path
                        type: '.war'
                    ]
                ], 
                credentialsId: 'nexus', 
                groupId: 'works.buddy.samples', 
                nexusUrl: 'http://52.77.248.42:8081/', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'my_repo_nexus', 
                version: '1.0'
            }
        }
    }
}
