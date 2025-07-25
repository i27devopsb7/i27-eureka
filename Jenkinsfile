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
                        -Dsonar.host.url=http://34.30.23.85:9000 \
                        -Dsonar.login=sqp_6dedf8d4494d456a2074e4b396122f04cd2e4259
                """
            }
        }
    }
}