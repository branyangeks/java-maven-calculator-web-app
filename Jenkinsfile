pipeline {
    agent any
    tools {
        maven 'M3'
        docker 'docker' 
    }
    stages {
        stage('Checkout Code') { 
            steps {
                checkout scm
            }
        }
        stage('JUnit Test') {
            steps {
                if (isUnix()) {
                    sh "'${mvnHome}/bin/mvn' clean test"
                } else {
                    bat(/"${mvnHome}\bin\mvn" clean test/)
                }
            }
        }
        stage('Integration Test') {
            steps{
                if (isUnix()) {
                sh "'${mvnHome}/bin/mvn' integration-test"
            } else {
                bat(/"${mvnHome}\bin\mvn" integration-test/)
            }
            }
        }
        /*
        stage('Performance Test') {
            if (isUnix()) {
                sh "'${mvnHome}/bin/mvn' cargo:start verify cargo:stop"
            } else {
                bat(/"${mvnHome}\bin\mvn" cargo:start verify cargo:stop/)
            }
        }
        */
        stage('Performance Test') {
            if (isUnix()) {
                sh "'${mvnHome}/bin/mvn' verify"
            } else {
                bat(/"${mvnHome}\bin\mvn" verify/)
            }
        }
        stage('Docker Build') {
            steps{
                sh 'docker build -t dockerregistry.io/calculatorapp/calculator:${BUILD_NUMBER} -t dockerregistry.io/calculatorapp/calculator:latest .'
            }        
        }
        stage('Docker Push') {
            steps{
            withDockerRegistry(credentialsId: 'docker_demo', toolName: 'docker', url: 'dockerhub.io') {
                    sh 'docker push dockerregistry.io/calculatorapp/calculator'
                }
            }

        }

        stage('Deploy') {
            timeout(time: 10, unit: 'MINUTES') {
                input message: 'Deploy this web app to production ?'
            }
            echo 'Deploy...'
        }
    }
}

   
