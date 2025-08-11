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
        // read the pom.xml file 
        POM_VERSION = readMavenPom().getVersion()
        POM_PACKAGING = readMavenPom().getPackaging()
        APPLICATION_NAME = "eureka"
        SONAR_URL = "http://34.30.23.85:9000"
        // SONAR_TOKEN = credentials('sonar_creds')

        DOCKER_HUB = "docker.io/i27devopsb7"
        DOCKER_CREDS = credentials('dockerhub_creds')
        //DOCKER_VM = "35.202.42.232"

    }

    stages {
        stage ('Build'){
            steps {
                // using maven
                echo "********** Building ${env.APPLICATION_NAME} Application *************"
                sh "mvn clean package -DskipTests=true"
            }
        }
        // stage ('Sonar') {
        //     steps {
        //         echo "************** Starting Sonar Scan **************"
        //         withSonarQubeEnv('SonarQube') {
        //             sh """
        //                 mvn clean verify sonar:sonar \
        //                     -Dsonar.projectKey=i27-eureka \
        //                     -Dsonar.host.url=${env.SONAR_URL}\
        //                     -Dsonar.login=${env.SONAR_TOKEN}
        //             """
        //         }
        //         timeout (time: 2, unit: "MINUTES") {
        //             script {
        //                 waitForQualityGate abortPipeline: true
        //             }
        //         }
                 





        //     }
        // }
        stage ('FormatBuild'){
            // existing i27-eureka-0.0.1-SNAPSHOT.jar
            // Destination i27-eureka-buildnumber-brnachname.jar
            steps {
                echo "Testing JAR Source: i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}"
                echo "Testing JAR Destination: i27-${env.APPLICATION_NAME}-${BUILD_NUMBER}-${BRANCH_NAME}.${env.POM_PACKAGING}"
            }
        }
        stage ('DockerBuildAndPush') {
            steps {
                script {
                    dockerBuildAndPush().call()
                }
                // docker.io/i27devopsb7/eureka:v
            }
        } 
        stage ('Deploy To Dev') {
            steps {
                script {
                    dockerDeploy('dev', '5761').call()
                }
            }
        }
        stage ('Deploy To test') {
            steps {
                script{
                    dockerDeploy('test', '6761').call()
                }
            }
        }
        stage ('Deploy To Stage') {
            steps {
                script{
                    dockerDeploy('stage', '7761').call()
                }
            }
        }
        stage ('Deploy To Prod') {
            steps {
                script{
                    dockerDeploy('prod', '8761').call()
                }
            }
        }
    }
}


def dockerDeploy(envDeploy, port){
    return {
        echo "Deploying to $envDeploy Environment"
            withCredentials([usernamePassword(credentialsId: 'john_docker_vm_creds', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                    // some block
                script {
                    try {
                        // stop the container
                        sh "sshpass -p $PASSWORD -v ssh -o StrictHostKeyChecking=no $USERNAME@$docker_vm_ip \"docker stop ${APPLICATION_NAME}-$envDeploy\""

                        // remove the container
                        sh "sshpass -p $PASSWORD -v ssh -o StrictHostKeyChecking=no $USERNAME@$docker_vm_ip \"docker rm ${APPLICATION_NAME}-$envDeploy\"" 
                    }
                    catch(err) {
                        echo "Error caught: $err"
                    }
                    // Creating a container
                    sh "sshpass -p $PASSWORD -v ssh -o StrictHostKeyChecking=no $USERNAME@$docker_vm_ip \"docker run --name ${APPLICATION_NAME}-$envDeploy -p $port:8761 -d ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:$GIT_COMMIT\""
                }
            } 
    }
}

def dockerBuildAndPush() {
    return {
        echo "************* Building the Docker image ***************"
        sh "cp target/i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} ./.cicd"
        sh "docker build --no-cache --build-arg JAR_SOURCE=i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} -t ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:$GIT_COMMIT ./.cicd"
        echo "******************************************** Docker Login *********************************"
        sh "docker login -u ${DOCKER_CREDS_USR} -p ${DOCKER_CREDS_PSW}"
        echo "******************************************** Docker Push *********************************"
        sh "docker push ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:$GIT_COMMIT"
    }
}

//  hostport:containerport

// dev > 5761:8761
// test > 6761:8761
// stage > 7761:8761
//prod > 8761:8761
