pipeline{
    agent any
    //tidy up the number of builds that are stored - this is limited due to memory issues
    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
        timestamps()
    }

    environment{

    }    

    tools{
        maven 'Maven3.5.0'
    }
    
    stages{
        //build the code
        stage('Build'){
            //ignore the main branch
            when { 
                not { 
                    branch 'main' 
                }
            }
            parallel{
                stage('Java'){
                    steps{
                        echo 'Building Java code.....'
                        sh "mvn -f ${workspace}/pipeline/pom.xml clean package -DskipTests"
                    }                    
                }
                stage('Windows'){
                    steps{
                        echo 'Building Windows code....'
                    }                    
                }
                stage('Database'){
                    steps{
                        echo 'Building Windows code....'
                    }                    
                }

            }
            post {
                failure{
                    echo 'The build has failed'
                }
            }
            
        }

        stage('Execute Tests'){
            parallel{
                stage('Database'){
                    //agent any
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
                    //agent any
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
                    //agent any
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
            //don't deploy until build has passed
            when {
                expression {
                    currentBuild.result == null || currentBuild.result == 'SUCCESS' 
                }
            }
            parallel{
                stage('Database'){
                    //agent any
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
                    //agent any
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
                    //agent any
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
    }
}