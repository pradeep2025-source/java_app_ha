pipeline {
    agent any

    

   
    environment {
        TOMCAT_URL = 'http://52.87.185.216:8000/'
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
                
                        cd /var/lib/jenkins/workspace/Javaproject with Jenkins file/calculator_app/target
                        cp -r /var/lib/jenkins/workspace/Javaproject with Jenkins file/calculator_app/target/*.war  /var/lib/tomcat10/webapps/ROOT
                                                               
                  }
            }
        }

    }

    post {
        always {
            // cleanWs()
            publishHTML (target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'calculator_app/target/jmeter/reports/CalculatorTestPlan',
                reportFiles: 'index.html',
                reportName: 'JMeter Report',
                ])

            sh '''
                ls -lrt
                tree
            '''
        }
    }

}
