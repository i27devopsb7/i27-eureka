// This pipeline is for Eureka microservice deployment
pipeline {
    agent {
        label 'java-slave'
    }

    // tools section 
    tools {
        maven 'maven-3.8.8'
        jdk 'JDK-17'
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