pipeline {

  agent any
  tools {
    maven 'Maven 3.8.6'
    jdk 'jdk8'
  }
  
  environment {
    APP_NAME = readMavenPom().getArtifactId()
    ARTIFACT_ID = readMavenPom().getArtifactId()
    GROUP_ID = readMavenPom().getGroupId()
    VERSION = readMavenPom().getVersion()
    //adding a comment for the commit test
    SCM_URL = scm.getUserRemoteConfigs()[0].getUrl()
    BRANCH = "${GIT_BRANCH.split("/")[1]}"
    RELEASE_NUMBER = "${BRANCH == 'release' ? "${GIT_BRANCH.split("/")[2]}" : ''}"
    RELEASE_BRANCH_NAME = "${BRANCH}" + "/" + "${RELEASE_NUMBER}"
    DEVELOP_BRANCH = "main"
    RELEASE_BRANCH = "release"
    BRANCH_NAME = "${BRANCH == 'release' ? "${RELEASE_BRANCH_NAME}" : 'main'}"

    DEPLOY_CREDS = credentials('deploy-anypoint-user')
    MULE_VERSION = '4.4.0'
    BG = "bd15e991-e67b-4eb9-9f41-7682ab63b4eb"
    WORKER = "Micro"
    M2SETTINGS = "/Users/smahadevan/.m2"
    
  }

  stages {
    stage('Build') {
      
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
          sh 'echo ${BRANCH_NAME}'
          checkout poll: false, scm: [$class: 'GitSCM', branches: [[name: "*/${BRANCH_NAME}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'Github', url: "${SCM_URL}"]]]
          sh 'mvn clean -DskipTests package'
        }
      }
    }

    stage('Deploy to exchange') {
      steps {
          sh 'echo ${exchange}'
          if(exchange.toBoolean()){
          sh "mvn clean deploy -Pexchange -DconnectedAppClientId=afdce4d6c4264c6d9edc93e400587f26 -DconnectedAppClientSecret=A40834Cf23b645408c6EEb9AcAD44497"
      }
        else {sh 'echo ${exchange}'}
      }
    }

    stage('Download jar from exchange') {
      steps {
          sh 'echo ${exchange}'
          if(exchange.toBoolean()){
          sh "mvn dependency:copy -Dartifact=bd15e991-e67b-4eb9-9f41-7682ab63b4eb:demo-project:1.0.0-SNAPSHOT:jar:mule-application -DoutputDirectory=./ -B -DconnectedAppClientId=afdce4d6c4264c6d9edc93e400587f26 -DconnectedAppClientSecret=A40834Cf23b645408c6EEb9AcAD44497"
      }
        else sh {'echo ${exchange}'}
      }
    }
    stage('Deploy to DEV') {
      steps {
        sh 'echo ${SCM_URL}'
        sh 'echo ${VERSION}'
        sh 'echo ${BRANCH_NAME}'
        sh 'echo ${WORKSPACE}'
        sh 'mvn mule:deploy -Dmule.artifact="./${APP_NAME}-${VERSION}-mule-application.jar" -DconnectedAppClientId=$connectedAppClientId -DconnectedAppClientSecret=$connectedAppClientSecret -DapplicationName=${APP_NAME} -Denvironment=Sandbox -Dtarget=mulesoft-dev -DbusinessGroupId=${GROUP_ID} -DmuleDeploy -DskipTests'
      }
    }
  }
}
