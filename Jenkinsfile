pipeline {
    agent any

    tools {
        maven 'maven_tool'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '2'))
    }

    environment {
        TOMCAT_URL = 'http://54.221.173.28:8080'
        CONTEXT_PATH = '/calculator'
    }

    stages {

        stage('Code Checkout with checkout') {
            steps {
                checkout([$class: 'GitSCM',
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'https://github.com/pradeepbrucelee/java_app_ha.git',
                        credentialsId: '5bf9bcf1-a7f6-4a2e-8859-c8f510a7cadd']]])
            }
        }

        stage('Unit Testing') {
            steps {
                sh '''
                    cd ./calculator_app/
                    mvn clean test
                '''
            }
        }

        stage('Integration Test') {
            steps {
                dir('./calculator_app') {
                    sh 'mvn integration-test'
                }
            }
        }

        stage('Performance Test - JMeter') {
            steps {
                dir('./calculator_app') {
                    sh 'mvn verify'
                }
            }
        }

        stage('Build Package') {
            steps {
                sh '''
                    cd ./calculator_app/
                    mvn package
                '''
            }
        }

        stage('Deploy-Tomcat') {
                        steps {
                script {
                    if ( DEPLOY_CHOICE == 'yes') {
                        echo "Deploying to Tomcat10: $TOMCAT_URL"
                        deploy adapters: [tomcat9(credentialsId: 'tomcat_manager', path: '', url: "${env.TOMCAT_URL}")],
                                         contextPath: "${env.CONTEXT_PATH}",
                                         war: 'calculator_app/target/calculator.war'
                    } else {
                        echo "Skipped deployment to Tomcat10: $TOMCAT_URL"
                    }
                }
            }
        }

    }
 }
