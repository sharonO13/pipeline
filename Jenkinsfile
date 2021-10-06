pipeline{
    agent any
    //tidy up the number of builds that are stored - this is limited due to memory issues
    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
        timestamps()
    }
    environment{
        ENV = "${env.BRANCH_NAME}"
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
                        echo "environment is ${env.ENV}"
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

        when{
            not{
                anyOf{
                    branch 'main'
                    branch 'release'
                }
            }
        }
        stage('Execute Tests'){
            parallel{
                stage('Database'){
                    //agent any
                    steps{
                        echo 'test the database'
                    }
                    post {
                        always {
                            echo 'capture results'
                        }
                    }
                }
                stage('Windows'){
                    //agent any
                    steps{
                        echo 'test the windows build with NUnit'
                    }
                    post {
                        always {
                            echo 'capture results'
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
                        echo 'deploy the database'
                    }
                    post {
                        always {
                            echo 'send an email on deployment'
                        }
                    }
                }
                stage('Windows'){
                    //agent any
                    steps{
                        echo 'deploy the database'
                    }
                    post {
                        always {
                            echo 'send an email on deployment'
                        }
                    }
                }
                stage('Linux'){
                    //agent any
                    steps{
                        echo 'deploy to docker container'
                    }
                    post {
                        always {
                            echo 'send email on deployment'
                        }
                    }
                } 
             }
        }      
    }
}