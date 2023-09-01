pipeline {
  agent any
  parameters{
    string(name: 'BRANCH', defaultValue: 'main', description:' anythings')
  }
  tools {
    maven 'Maven'
    'org.jenkinsci.plugins.docker.commons.tools.DockerTool' 'docker'
  }
  environment {
    SONAR_URL = "http://10.0.0.241:9000"
  }
  stages {
    stage("Source") {
      steps {
        git branch: params.BRANCH, url: 'https://github.com/tonux/tp-master-sir-2023.git'
      }
    }
    stage("Parallel Stages") {
      parallel {
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

    stage('Build Docker image') {
          steps{
                sh 'docker build -t ucadsir/tp-msir:latest .'
          }
    }
    stage('Login to Docker Hub') {
         steps{
             sh 'docker login -u "ucadsir" -p "Ucadsir@2023"'
         }
    }
    stage('Push to Docker Hub') {
        steps{
            sh 'docker push ucadsir/tp-msir:latest'
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
        body: "The build was successful. No further action is required.\n \n Build URL: $BUILD_URL",
        recipientProviders: [culprits(), developers()],
        replyTo: "sambndongo@gmail.com",
        to: "sambndongo@gmail.com"
      )
    }
    failure {
      emailext(
        subject: "Build Failed: ${currentBuild.fullDisplayName}",
        body: "The build has failed. Please investigate and take necessary action. \n \n Build URL: $BUILD_URL",
        recipientProviders: [culprits(), developers()],
        replyTo: "sambndongo@gmail.com",
        to: "sambndongo@gmail.com"
      )
    }

  }
}