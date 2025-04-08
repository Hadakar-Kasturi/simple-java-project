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

        stage('Clean .m2 Repository') {
            steps {
                // Clean the local Maven repository cache to avoid corrupted dependencies
                sh 'rm -rf ~/.m2/repository/org/sonatype/plexus'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('my_sonar') {
                    sh """
                    mvn clean install -X
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
                        // This step waits for the quality gate to pass
                        waitForQualityGate abortPipeline: false
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // Compile the project
                sh 'mvn clean compile -X'
            }
        }

        stage('Package') {
            steps {
                // Package the project
                sh 'mvn clean package -X'
            }
        }

        stage('Artifact Upload') {
            steps {
                // Upload the artifact to Nexus repository
                nexusArtifactUploader artifacts: [[artifactId: 'my_repo_nexus', classifier: '', file: 'target/my_repo_nexus-1.0.war', type: 'war']], 
                credentialsId: 'nexus', groupId: 'works.buddy.samples', 
                nexusUrl: 'http://52.77.248.42:8081/', 
                nexusVersion: 'nexus3', protocol: 'http', 
                repository: 'my_repo_nexus', version: '1.0'
            }
        }
    }
}
