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
                git branch: 'main', credentialsId: 'git_token', url: 'https://github.com/Hadakar-Kasturi/repo_mine.git'
            }
        }

        stage('SonarQube') {
            steps {
                  
                   withSonarQubeEnv('my_sonar') { 
                   sh """
                   mvn sonar:sonar \
                   -Dsonar.projectKey="demo-project-${env.BUILD_NUMBER}"\
                   -Dsonar.projectName="demo-Project - Build #${env.BUILD_NUMBER}"\
                   -Dsonar.qualitygate.wait=true\
                   -Dsonar.projectVersion=${BUILD_ID}\
                      """
                   }
                
            }
            
        }
                   
           stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline:false
                    
                }
            }
            
            }
           }
    }
}
