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
        booleanParam(name: 'report', defaultValue: false, description: 'Enable Allure reporting')

        choice(name: 'env', choices: ['e2e', 'preprod', 'test'], description: 'Pick env')
    }

    stages {
        stage('Install Allure Reporter') {
            steps {
                sh 'npm install -g newman-reporter-allure'
            }
        }

        stage('lancer le test') {
            steps {
                script {
                    sh 'mkdir -p allure-results'

                    def reporterArgs = ""
                    if (params.report) {
                        reporterArgs = " -r cli,allure --reporter-allure-export allure-results"
                    }

                    if (params.execution_default_collection) {
                        sh "newman run collection.json${reporterArgs}"
                    }

                    if (params.default_execution_for_all) {
                        sh "newman run collection.json${reporterArgs}"
                        sh "newman run Collection-Multi.postman_collection.json -e e2e.postman_environment.json${reporterArgs}"
                        sh "newman run Collection-Multi.postman_collection.json -e preprod.postman_environment.json${reporterArgs}"
                        sh "newman run Collection-Test.postman_collection.json -e test.postman_environment.json${reporterArgs}"
                    }

                    if (params.excute_specific_collection) {
                        switch (params.env) {
                            case 'e2e':
                                sh "newman run Collection-Multi.postman_collection.json -e e2e.postman_environment.json${reporterArgs}"
                                break
                            case 'preprod':
                                sh "newman run Collection-Multi.postman_collection.json -e preprod.postman_environment.json${reporterArgs}"
                                break
                            case 'test':
                                sh "newman run Collection-Test.postman_collection.json -e test.postman_environment.json${reporterArgs}"
                                break
                        }
                    }

                    // Fix permissions avant stash
                    if (params.report) {
                        sh 'chmod -R 777 allure-results'
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                if (params.report) {
                    allure([
                        includeProperties: false,
                        jdk: '',
                        properties: [],
                        reportBuildPolicy: 'ALWAYS',
                        results: [[path: 'allure-results']]
                    ])
                }
            }
        }
    }
}