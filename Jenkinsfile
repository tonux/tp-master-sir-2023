pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment{
        SONAR_URL="http://10.0.0.241:9000"
    }
    stages {
        stage("Source") {
            steps {
                git branch: 'main', url: 'https://github.com/tonux/tp-master-sir-2023.git'
            }
        }
        stage("Parallel Stages"){
             parallel{
                stage('Maven version') {
                                        steps {
                                            sh "mvn --version"
                                        }
                             }
                              stage("Build") {
                                     steps {
                                         sh 'mvn clean org.jacoco:jacoco-maven-plugin:prepare-agent install'
                                     }
                                }
             }
        }
        stage("SonarQube Analysis") {
            steps {
                sh 'mvn sonar:sonar -Dsonar.host.url=$SONAR_URL'
            }
        }
        stage('Approve Deployment') {
            input {
                message "Do you want to proceed for deployment?"
            }
            steps {
                sh 'echo "Deploying into Server"'
            }
        }
    }
    post {
        aborted {
            echo "Sending message to agent"
        }
        success {
               emailext(
                   subject: "Build Successful: ${currentBuild.fullDisplayName}",
                   body: "The build was successful. No further action is required.",
                   recipientProviders: [culprits(), developers()],
                   replyTo: "sambndongo@gmail.com",
                   to: "sambndongo@gmail.com"
               )
           }
           failure {
               emailext(
                   subject: "Build Failed: ${currentBuild.fullDisplayName}",
                   body: "The build has failed. Please investigate and take necessary action.",
                   recipientProviders: [culprits(), developers()],
                   replyTo: "sambndongo@gmail.com",
                   to: "sambndongo@gmail.com"
               )
           }

    }
}