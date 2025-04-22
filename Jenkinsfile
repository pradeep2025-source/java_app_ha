pipeline {
    agent any

    tools {
        maven 'maven_tool'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '2'))
    }

    environment {
        TOMCAT_URL = 'http://52.73.151.57:8080'
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
                    sh '''
                    sudo su - root
                    cd /var/lib/jenkins/workspace/Javaproject with Jenkins file/calculator_app/target
                    cp -r /var/lib/jenkins/workspace/Javaproject with Jenkins file/calculator_app/target/calculator.war /var/lib/tomcat10/webapps/ROOT
                    '''
                }
            }
        }

    }
 }
