#!/usr/bin/groovy
@Library('github.com/fabric8io/fabric8-pipeline-library@master')
def utils = new io.fabric8.Utils()
clientsNode{
  def envStage = utils.environmentNamespace('stage')
  def newVersion = ''

  checkout scm
  stage('Build Release')
  echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
  if (!fileExists ('Dockerfile')) {
    writeFile file: 'Dockerfile', text: 'FROM microsoft/dotnet:onbuild'
  }

  newVersion = performCanaryRelease {}

  def rc = getDeploymentResources {
    port = 5000
    label = 'dotnet'
    icon = 'https://cdn.rawgit.com/fabric8io/fabric8/392b07b/website/src/images/logos/dotnet.png'
    version = newVersion
    imageName = clusterImageName
  }

  stage('Rollout to Stage')
  kubernetesApply(file: rc, environment: envStage)
}
