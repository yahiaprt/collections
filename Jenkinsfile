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

        choice(name: 'env', choices: ['e2e', 'preprod', 'test'], description: 'Pick env')
      //  choice(name: 'collection_selection', choices: ['Collection-Multi.postman_collection', 'Collection-Test.postman_collection', 'collection'], description: 'Pick collection')
    }

    stages {
        stage('lancer le test') {
            steps {
                script {
                    if (params.execution_default_collection) {
                        sh 'newman run collection.json'
                    }
                    
                     if (params.default_execution_for_all) {
                        sh 'newman run collection.json'
                        sh 'newman run Collection-Multi.postman_collection.json -e e2e.postman_environment.json'
                        sh 'newman run Collection-Multi.postman_collection.json -e preprod.postman_environment.json'
                        sh 'newman run Collection-Test.postman_collection.json -e test.postman_environment.json'
                    } 
                    if(params.excute_specific_collection)
                        switch (params.env) {
                            case 'e2e':
                                sh 'newman run Collection-Multi.postman_collection.json -e e2e.postman_environment.json'
                                break
                            case 'preprod':
                                sh 'newman run Collection-Multi.postman_collection.json -e preprod.postman_environment.json'
                                break
                            case 'test':
                                sh 'newman run Collection-Test.postman_collection.json -e test.postman_environment.json'
                                break
                        }
                    }
                }
            }
        }
    }
}

 //pipeline {
    agent {
        docker
        { 
            image 'postman/newman:latest'
            args '-u=root --entrypoint='
        }  
    }
    parameters {

 
 
        booleanParam(name: 'default_exectution_for_all ', defaultValue: false, description: 'Toggle this value')
        booleanParam(name: 'excution_default_collection ', defaultValue: false, description: 'Toggle this value')

        choice(name: 'collection', choices: ['e2e', 'preprod', 'test'], description: 'Pick env')
                choice(name: 'env', choices: ['e2e', 'preprod', 'test'], description: 'Pick env')

        choice(name: 'Collection-Multi.postman_collection ENV', choices: ['e2e', 'preprod'], description: 'Pick env')
        choice(name: 'Collection-Test.postman_collection ENV', choices: ['test'], description: 'Pick env')

        choice(name: 'collection_selection', choices: ['Collection-Multi.postman_collection'
        , 'Collection-Test.postman_collection', 'collection'], description: 'Pick collection')
    }

    stages {
        stage('lancer le test') {
            steps {
                if(excution_default_collection) {
                    sh 'newman run  collection.js'
                }

                if(booleanParam.default_exectution_for_all){
                  sh 'newman run  collection.js'
                  sh 'newman run  Collection-Multi.postman_collection.js -e e2e.postman_environment'   
                  sh 'newman run  Collection-Multi.postman_collection.js -e preprod.postman_environment'  
                  sh 'newman run  Collection-Test.postman_collection.js -e test.postman_environment' 
                
                } else {
                switch(choice.env) {
                    case e2e:
                       sh 'newman run  Collection-Multi.postman_collection -e e2e.postman_environment'  
                    break
                      case preprod:
                       sh 'newman run  Collection-Multi.postman_collection -e preprod.postman_environment'  

                    break
                      case test:
                     sh 'newman run  Collection-Test.postman_collection -e test.postman_environment'  

                    break
                }
                }
            }
        }
    }
//}
