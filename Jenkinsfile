pipeline {
    agent any   // node par défaut → a Java, nécessaire pour le plugin Allure en post

    parameters {
        booleanParam(name: 'default_execution_for_all', defaultValue: false, description: 'Run all collections/envs')
        booleanParam(name: 'execution_default_collection', defaultValue: false, description: 'Run only the default collection')
        booleanParam(name: 'excute_specific_collection', defaultValue: false, description: 'Run only a specific collection/env')
        booleanParam(name: 'report', defaultValue: false, description: 'Generate and publish the Allure report')
        choice(name: 'env', choices: ['e2e', 'preprod', 'test'], description: 'Pick env')
    }

    environment {
        ALLURE_DIR = 'allure-results'
    }

    stages {
        stage('lancer le test') {
            agent {
                docker {
                    image 'postman/newman:latest'
                    args '-u root --entrypoint='
                    reuseNode true   // partage le workspace du node parent -> pas besoin de stash/unstash
                }
            }
            steps {
                script {
                    sh 'npm install -g newman-reporter-allure'

                    def reporterArgs = params.report ? "--reporters cli,allure --reporter-allure-export ${ALLURE_DIR}" : ''

                    if (params.execution_default_collection) {
                        sh "newman run collection.json ${reporterArgs}"
                    }

                    if (params.default_execution_for_all) {
                        sh "newman run collection.json ${reporterArgs}"
                        sh "newman run Collection-Multi.postman_collection.json -e e2e.postman_environment.json ${reporterArgs}"
                        sh "newman run Collection-Multi.postman_collection.json -e preprod.postman_environment.json ${reporterArgs}"
                        sh "newman run Collection-Test.postman_collection.json -e test.postman_environment.json ${reporterArgs}"
                    }

                    if (params.excute_specific_collection) {
                        switch (params.env) {
                            case 'e2e':
                                sh "newman run Collection-Multi.postman_collection.json -e e2e.postman_environment.json ${reporterArgs}"
                                break
                            case 'preprod':
                                sh "newman run Collection-Multi.postman_collection.json -e preprod.postman_environment.json ${reporterArgs}"
                                break
                            case 'test':
                                sh "newman run Collection-Test.postman_collection.json -e test.postman_environment.json ${reporterArgs}"
                                break
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                if (params.report) {
                    archiveArtifacts artifacts: "${ALLURE_DIR}/**", allowEmptyArchive: true
                    allure includeProperties: false, jdk: '', results: [[path: "${ALLURE_DIR}"]]
                }
            }
        }
    }
}


/*
pipeline {
    agent {
        docker {
            image 'postman/newman:latest'
            args '-u root --entrypoint='
        }
    }

    parameters {
        booleanParam(name: 'default_execution_for_all', defaultValue: false, description: 'Run all collections/envs')
        booleanParam(name: 'execution_default_collection', defaultValue: false, description: 'Run only the default collection')
        booleanParam(name: 'excute_specific_collection', defaultValue: false, description: 'Run only the default collection')
        booleanParam(name: 'report', defaultValue: false, description: 'Run only the default collection')

        choice(name: 'env', choices: ['e2e', 'preprod', 'test'], description: 'Pick env')
      //  choice(name: 'collection_selection', choices: ['Collection-Multi.postman_collection', 'Collection-Test.postman_collection', 'collection'], description: 'Pick collection')
    }

    stages {
        stage('lancer le test') {
            steps {
             
                script {  
                       sh 'npm install -g newman-reporter-allure'
                  sh 'mkdir -p allure-results'

                  let valuee = ""
                  if(allure == true) {
                    valuee = " --reporters cli,allure --reporter-allure-export output/allure-results"
                  } 
                    if (params.execution_default_collection) {
                        sh 'newman run collection.json' + valuee
                    }
                    
                     if (params.default_execution_for_all) {
                        sh 'newman run collection.json' + valuee
                        sh 'newman run Collection-Multi.postman_collection.json -e e2e.postman_environment.json' + valuee
                        sh 'newman run Collection-Multi.postman_collection.json -e preprod.postman_environment.json' + valuee
                        sh 'newman run Collection-Test.postman_collection.json -e test.postman_environment.json' valuee
                    } 
                    if(params.excute_specific_collection)
                        switch (params.env) {
                            case 'e2e':
                                sh 'newman run Collection-Multi.postman_collection.json -e e2e.postman_environment.json' + valuee
                                break
                            case 'preprod':
                                sh 'newman run Collection-Multi.postman_collection.json -e preprod.postman_environment.json' + valuee
                                break
                            case 'test':
                                sh 'newman run Collection-Test.postman_collection.json -e test.postman_environment.json' + valuee
                                break
                        }

                        if(allure) {
                            stash name: 'allure-results', includes: 'allure-results/*'
                        }
                    }
                }
            }
        }

 post{
        always{
            script{
                if(params.ALLURE){
                    unstash 'allure-results'
                    archiveArtifacts 'allure-results/*'
                    allure includeProperties: false,
                           jdk: '',
                           results: [[path: 'allure-results/']]
                }
            }
        }
    }



    }


*/