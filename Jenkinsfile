pipeline {
  agent {
    label "jenkins-agent"
  }
  tools {
    jdk 'Java17'
    maven 'Maven3'
  }

  environment {
    APP_NAME = "complete-CICD-e2e-pipeline" // Adjusted to follow naming pattern
    RELEASE = "1.0.0"
    DOCKER_USER = "mayank56"
    DOCKER_PASS = "Dockercred"
    IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}" // Adjusted to follow naming pattern
    IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
  }

  stages {
    stage("clear the workspace") {
      steps {
        cleanWs()
      }
    }

    stage("checkout from SCM") {
      steps {
        git branch: 'main', credentialsId: "github", url: "https://github.com/MayankSingh56/complete-prodcution-e2e-pipeline"
      }
    }

    stage("build the code") {
      steps {
        sh 'mvn clean package'
      }
    }

    stage("Test") {
      steps {
        sh 'mvn test'
      }
    }

    stage("Sonarqube configure") {
      steps {
        script {
          withSonarQubeEnv(credentialsId: "sonar-jenkins-token") {
            sh "mvn sonar:sonar"
          }
        }
      }
    }

    stage("qualitygates") {
      steps {
        script {
          waitForQualityGate abortPipeline: false, credentialsId: "sonar-jenkins-token"
        }
      }
    }

    stage("docker build and push the image") {
      steps {
        script {
          docker.withRegistry('', DOCKER_PASS) {
            docker_image = docker.build "${IMAGE_NAME}:${IMAGE_TAG}" // Corrected image tag format
          }
          docker.withRegistry('', DOCKER_PASS) {
            docker_image.push("${IMAGE_TAG}")
            docker_image.push('latest')
          }
        }
      }
    }
  }
}
