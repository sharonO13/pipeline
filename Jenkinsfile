pipeline{
    agent any
    //build every 5 minutes monday to friday
    triggers {
        cron('H/5 * * * 1-5')
        pollSCM('*/2 * * * *')
    }
    //tidy up the number of builds that are stored - this is limited due to memory issues
    options {
        buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '2'))
        timeout(time: 5, unit: 'MINUTES')
        timestamps()
    }
    //get the current branch name to determine post steps
    environment{
        ENV = "${env.BRANCH_NAME}"
    }

    parameters{
        string(name:'TEST', description: 'testing parameters')
    }

    tools{
        maven 'Maven3.5.0'
    }
    
    stages{
        //build the code
        stage('Build'){
            //ignore the main branch and do not build if empty
            when { 
                not { 
                    branch 'main'                     
                }
            }
            parallel{
                stage('Java'){
                    steps{
                        echo 'Building Java code.....'
                        echo "parameter to use ${params.TEST}"
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

        //execute tests except on main and release branches
        stage('Execute Tests'){
            when{
                not{
                    anyOf{
                        branch 'main'
                        branch 'release'
                    }
                }
            }
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
                    always{
                        junit '**/surefire-reports/*.xml'
                    }
                }
            } 
            }
        }

        //require approval to deploy to QA, stage, release to put controls on quality
        stage('Deploy approval'){
            when{
                anyOf{
                    expression{ "${env.ENV}" == 'qa' }
                    expression{ "${env.ENV}" == 'stage' }
                    expression{ "${env.ENV}" == 'release' }
                }
            }
            steps{
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                    input(id: "Deploy", message: "Deploy ${env.ENV}?", ok: 'Deploy')
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
                        echo 'update the database'
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
                        echo 'deploy to the windows server'
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
