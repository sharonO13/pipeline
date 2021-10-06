pipeline{
    agent any

    tools{
        maven 'Maven3.5.0'
    }
    
    stages{
        stage('Build'){
            steps{
                sh 'mvn -B -DskipTests -f /pipeline/pipeline clean package'
            }
        }

        stage('Test'){
            steps{
                sh 'mvn -f /pipeline/pipeline test'
            }
        }

        stage('Deploy'){
            steps{
                echo 'Deploy'
            }
        }      
    }
}