pipeline{
    agent any

    tools{
        maven 'Maven3.5.0'
    }
    
    stages{
        stage('Build'){
            steps{
                sh "mvn -f ${workspace}/pipeline/pom.xml clean package -DskipTests"
            }
        }

        stage('Test'){
            steps{
                sh "mvn -f ${workspace}/pipeline/pom.xml test"
            }
            post {
                always {
                    archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
                    junit 'build/reports/**/*.xml'
                }
            }
        }

        stage('Deploy'){
            steps{
                echo 'Deploy'
            }
        }      
    }
}