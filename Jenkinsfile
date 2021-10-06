pipeline{

    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
    }
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

        stage('Execute Tests'){
            stage('Run on Linux'){
                parallel{
                    agent{
                        label 'Database'
                    }
                    steps{
                        sh "mvn -f ${workspace}/pipeline/pom.xml test"
                    }
                    post {
                        always {
                            junit '**/surefire-reports/*.xml'
                        }
                    }
                    agent{
                        label 'Windows'
                    }
                    steps{
                        sh "mvn -f ${workspace}/pipeline/pom.xml test"
                    }
                    post {
                        always {
                            junit '**/surefire-reports/*.xml'
                        }
                    }
                    agent{
                        label 'Linux'
                    }
                    steps{
                        sh "mvn -f ${workspace}/pipeline/pom.xml test"
                    }
                    post {
                        always {
                            junit '**/surefire-reports/*.xml'
                        }
                    }
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