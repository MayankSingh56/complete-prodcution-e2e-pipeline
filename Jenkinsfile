pipeline {
  agent {
    label "jenkins-agent"
  }
  tools {
    jdk 'Java17'
    maven 'Maven3'
  }

  environment {
    APP_NAME = "complete-cicd-e2e-pipeline" // Adjusted to lowercase
    RELEASE = "1.0.0"
    DOCKER_USER = "mayank56"
    DOCKER_PASS = "Dockercred"
    IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}" // Adjusted to lowercase
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

    stage("Trivy Scan") {
            steps {
                script {
		   sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image mayank56/complete-cicd-e2e-pipeline:1.0.0-17 --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
                }
            }

        }
  }
}
