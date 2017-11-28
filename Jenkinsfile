#!/usr/bin/groovy
@Library('github.com/fabric8io/fabric8-pipeline-library@master')
def canaryVersion = "1.0.${env.BUILD_NUMBER}"
def utils = new io.fabric8.Utils()
def label = "buildpod.${env.JOB_NAME}.${env.BUILD_NUMBER}".replace('-', '_').replace('/', '_')
def envProd = utils.environmentNamespace('run')
def envStage = utils.environmentNamespace('stage')
def stashName = ""
mavenNode {
  checkout scm
  if (utils.isCI()){

    mavenCI{}
    
  } else if (utils.isCD()){
    echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
    container(name: 'maven') {

      stage('Build Release'){
        mavenCanaryRelease {
          version = canaryVersion
        }
      }
      
      stage('Rollout to Stage'){
        kubernetesApply(environment: envStage)
        //stash deployments
        stashName = label
        stash includes: '**/*.yml', name: stashName
      }
    }
  }
}
