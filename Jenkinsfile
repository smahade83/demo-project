pipeline {

  agent any
  tools {
    maven 'Maven 3.8.7'
    jdk 'jdk8'
  }
  environment {
    //adding a comment for the commit test
    DEPLOY_CREDS = credentials('deploy-anypoint-user')
    MULE_VERSION = '4.4.0'
    BG = "bd15e991-e67b-4eb9-9f41-7682ab63b4eb"
    WORKER = "Micro"
    M2SETTINGS = "/Users/smahadevan/.m2"
  }
  stages {
    stage('Build') {
      steps {
            sh 'mvn clean -DskipTests package'
      }
    }

    stage('Test') {
      steps {
          sh "mvn test"
      }
    }

     stage('Deploy Development') {
      environment {
        ENVIRONMENT = 'Sandbox'
        APP_NAME = 'demo-project'
      }
      steps {
            sh 'mvn -DskipTests deploy -DmuleDeploy -Dmule.version="4.4.0" -Danypoint.username="smahadevan_mulesoft" -Danypoint.password="Acumen123" -Dcloudhub.app="demo-project"'
      }
    }
    }
  }
