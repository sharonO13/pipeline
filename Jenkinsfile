pipeline{
    agent any

    tools{
        maven 'Maven3.5.0'
    }
    
    stages{
        stage('Build'){
            steps{
                sh 'mvn -f /pipeline/pipeline/pom.xml clean install'
            }
        }

        stage('Test'){
            steps{
                sh 'mvn -f /pipeline/pipeline/pom.xml test'
            }
        }

        stage('Deploy'){
            steps{
                echo 'Deploy'
            }
        }      
    }
}