pipeline {
  agent {
    label "jenkins-agent"
  }
  tools {
    jdk 'Java17'
    maven 'Maven3'
  }

  stages {
    stage ("clear the workspace") {
     steps {
       cleanWs()
     }
    }

    
    stage ("checkout from SCM") {
     steps {
       git branch: 'main' , credentialsId: "github" , url: "https://github.com/MayankSingh56/complete-prodcution-e2e-pipeline"
     }
    }

    stage ("build the code") {
      steps {
        sh 'mvn clean package'
      }
    }

    stage ("Test") {
      steps {
        sh 'mvn test'
      }
    }

    stage ("Sonarqube configure") {
      steps {
        script {
        withSonarQubeEnv(credentialsId: "sonar-jenkins-token") {
        sh "mvn sonar:sonar"
        }
        }
      }
    }
  }
}