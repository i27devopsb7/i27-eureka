// This pipeline is for Eureka microservice deployment
pipeline {
    agent {
        label 'java-slave'
    }

    // tools section 
    tools {
        maven 'maven-3.8.9'
        jdk 'JDK-17'
    }

    // environment 
    environment {
        APPLICATION_NAME = "eureka"
        SONAR_URL = "http://34.30.23.85:9000"
        SONAR_TOKEN = credentials('sonar_creds')
    }

    stages {
        stage ('Build'){
            steps {
                // using maven
                echo "********** Building ${env.APPLICATION_NAME} Application *************"
                sh "mvn clean package -DskipTests=true"
            }
        }
        stage ('Sonar') {
            steps {
                echo "************** Starting Sonar Scan **************"
                sh """
                    mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=i27-eureka \
                        -Dsonar.host.url=${env.SONAR_URL}\
                        -Dsonar.login=${env.SONAR_TOKEN}
                """
            }
        }
    }
}