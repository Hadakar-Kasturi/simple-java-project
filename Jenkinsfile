pipeline {
    agent any

    tools {
        maven 'maven'
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
                   -Dsonar.projectKey="Test-project-${env.BUILD_NUMBER}"\
                   -Dsonar.projectName="Test-Project - Build #${env.BUILD_NUMBER}"\
                   -Dsonar.qualitygate.wait=true\
                   -Dsonar.projectVersion=${BUILD_ID}\
                    """
                      
                  //    sh """
    //mvn clean verify sonar:my_sonar \
      //  -Dsonar.projectKey=Test1 \
        //-Dsonar.host.url=http://52.221.230.199:9000/ \
        //-Dsonar.login=sqa_359f17b53e35aa3a10fe5d549b8b977fe8493f33 \
        //-Dsonar.qualitygate.wait=true\
  //      -Dsonar.projectVersion=${BUILD_ID}
//"""

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
           
           stage('Build'){
               steps{
               sh 'mvn clean compile'
               }
           }
            stage('Package'){
               steps{
                   sh 'mvn clean package'
               }
           }
       
           stage ('Nexus Upload'){
               steps {
                    nexusArtifactUploader artifacts: [[artifactId: 'my_repo_nexus', classifier: '', file: 'target//my_repo_nexus-1.0.war', type: '.war']], credentialsId: 'nexus', groupId: 'works.buddy.samples', nexusUrl: 'http://52.77.248.42:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'my_repo_nexus', version:'${BUILD_ID}'
               }
           }
    }
}
