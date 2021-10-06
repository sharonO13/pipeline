pipeline{

    When{
        Not{
            branch 'Release'
        }
    }
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
            parallel{
                stage('Database'){
                    agent any
                    steps{
                        sh "mvn -f ${workspace}/pipeline/pom.xml test"
                    }
                    post {
                        always {
                            junit '**/surefire-reports/*.xml'
                        }
                    }
                }
                stage('Windows'){
                    agent any
                    steps{
                        sh "mvn -f ${workspace}/pipeline/pom.xml test"
                    }
                    post {
                        always {
                            junit '**/surefire-reports/*.xml'
                        }
                    }
                }
                stage('Linux'){
                    agent any
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