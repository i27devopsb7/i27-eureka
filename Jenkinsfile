// This pipeline is for Eureka microservice deployment
pipeline {
    agent {
        label 'java-slave'
    }
    stages {
        stage ('Build'){
            steps {
                // using maven
                echo "********** Building Eureka Application *************"
                sh "mvn clean package"
            }
        }
    }
}