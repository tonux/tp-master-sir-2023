pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage("Source") {
            steps {
                git branch: 'main', url: 'https://github.com/tonux/tp-master-sir-2023.git'
            }
        }
        stage("Build") {
            steps {
                sh 'mvn clean org.jacoco:jacoco-maven-plugin:prepare-agent install'
            }
        }
        stage("SonarQube Analysis") {
            steps {
                sh 'mvn sonar:sonar'
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
        always {
            script {
                def coverageThreshold = 80
                def jacocoReport = load("target/site/jacoco/jacoco.xml")
                def lineCoverage = jacocoReport.'counter'.find { it.@type == 'LINE' }?.@missed
                def totalLines = jacocoReport.'counter'.find { it.@type == 'LINE' }?.@covered.toInteger() + lineCoverage.toInteger()
                def coveragePercentage = (lineCoverage.toInteger() / totalLines.toInteger()) * 100

                echo "Line Coverage: ${coveragePercentage}%"

                if (coveragePercentage < coverageThreshold) {
                    error "Test coverage is less than ${coverageThreshold}%. Aborting the pipeline."
                }
            }
        }
        aborted {
            echo "Sending message to agent"
        }
        failure {
            echo "Sending message to agent"
        }
        success {
            echo "Sending message to agent"
        }
    }
}
