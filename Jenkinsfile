#!/usr/bin/env groovy
@Library('github.com/stakater/fabric8-pipeline-library@ahmad')

def localItestPattern = ""
try {
    localItestPattern = ITEST_PATTERN
} catch (Throwable e) {
    localItestPattern = "*KT"
}

def localFailIfNoTests = ""
try {
    localFailIfNoTests = ITEST_FAIL_IF_NO_TEST
} catch (Throwable e) {
    localFailIfNoTests = "false"
}

def versionPrefix = ""
try {
    versionPrefix = VERSION_PREFIX
} catch (Throwable e) {
    versionPrefix = "1.0"
}

def canaryVersion = "${versionPrefix}.${env.BUILD_NUMBER}"
def fabric8Console = "${env.FABRIC8_CONSOLE ?: ''}"
def utils = new io.fabric8.Utils()
def label = "buildpod.${env.JOB_NAME}.${env.BUILD_NUMBER}".replace('-', '_').replace('/', '_')

def envStage = utils.environmentNamespace('stage')
def envProd = utils.environmentNamespace('run')
def stashName = ""
def deploy = false

// TODO # 1 : use different profile dev or prod
// TODO # 2 : use config maps
// TODO # 3 : the code is being checked out multiple times; that should be fixed! must be checked out only once
// TODO # 4 : run performance tests ( gatling ) and save stats
// TODO # 5 : the artifacts should be archived & should be browse'able from Jenkins!

clientsNode(clientsImage: 'fabric8/builder-clients:latest') {


    container(name: 'clients') {

        stage("checkout") {
            checkout scm
        }


    sh "git remote set-url origin git@github.com:stakater-spring-microservice/MovieManager.git"
    sh "git config user.email admin@stakater.com"
    sh "git config user.name stakater-release"
    sh 'chmod 600 /root/.ssh-git/ssh-key'
    sh 'chmod 600 /root/.ssh-git/ssh-key.pub'
    sh 'chmod 700 /root/.ssh-git'

        stage("push") {
        sh "git tag -fa v${canaryVersion} -m 'Release version ${canaryVersion}'"
        sh "git push origin v${canaryVersion}"
        }
    }

//        stage("release") {
//            push(canaryVersion)
//        }
}

//mavenNode(mavenImage: 'openjdk:8') {

//    ws ('pipelines'){

//        stage("checkout") {
//            checkout scm
//        }

//        def release = load 'release.groovy'

//        stage 'release'
//        release.push(canaryVersion)
//    }

//    container(name: 'maven') {

//        stage("checkout") {
//            checkout scm
//        }


//        stage("clean") {
//            sh 'chmod +x mvnw'
//            sh './mvnw clean'
//        }

//        stage('install tools') {
//            sh './mvnw com.github.eirslett:frontend-maven-plugin:install-node-and-yarn -DnodeVersion=v6.11.1 -DyarnVersion=v0.27.5'
//        }

//        stage('yarn install') {
//            sh './mvnw com.github.eirslett:frontend-maven-plugin:yarn'
//        }

//        stage('SonarQube analysis') {
//          withSonarQubeEnv('sonarqube') {
            // requires SonarQube Scanner for Maven 3.2+
//            sh "./mvnw -Dsonar.host.url=${env.SONAR_HOST_URL} org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar"
//          }
//        }

//        stage('Canary Release'){
//            mavenCanaryRelease {
//              version = canaryVersion
//            }
//            release.push(canaryVersion)
//        }

//        stage('Integration Testing') {
//            mavenIntegrationTest {
//              environment = 'Test'
//              failIfNoTests = localFailIfNoTests
//              itestPattern = localItestPattern
//            }
//        }

//        stage('Rollout to Stage') {
//            kubernetesApply(environment: envStage)
            // TODO: figure out why to stash deployments?
            // TODO: from where does the stash command comes?
            //stash deployments
//            stashName = label
//            stash includes: '**/*.yml', name: stashName
//        }
//    }
//}


// TODO: Ensure webhook for jenkins (http://jenkins/sonarqube-webhook/) is added in sonarqube
// No need to occupy a node
//stage("Quality Gate"){
//  timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
//    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
//    if (qg.status != 'OK') {
//      error "Pipeline aborted due to quality gate failure: ${qg.status}"
//    }
//  }
//}


def push(version) {


    def releaseVersion = version

    sh "git remote set-url origin git@github.com:stakater-spring-microservice/MovieManager.git"
    sh "git config user.email admin@stakater.com"
    sh "git config user.name stakater-release"
    sh 'chmod 600 /root/.ssh-git/ssh-key'
    sh 'chmod 600 /root/.ssh-git/ssh-key.pub'
    sh 'chmod 700 /root/.ssh-git'

    container(name: 'clients') {

        stage("checkout") {
            checkout scm
        }

//    sh "git tag ${env.JOB_NAME}-${config.version}"
//    sh "git push origin --tags"
//        sh "git push origin ${env.JOB_NAME}-${config.version}"

        stage("push") {
        sh "git tag -fa v${releaseVersion} -m 'Release version ${releaseVersion}'"
        sh "git push origin v${releaseVersion}"
        }
    }
}
