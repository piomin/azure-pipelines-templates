### Usage example

```yaml
resources:
  repositories:
    - repository: templates
      type: github
      endpoint: piomin
      name: piomin/azure-pipelines-templates
      ref: refs/heads/master

trigger:
  - master

pool:
  name: Azure Pipelines
  
stages:
  - template: java-build-stage.yml@templates
    parameters:
      appName: 'sample-spring-kotlin-microservice'
      feedName: 'pminkows'
  - template: s2i-build-image-stage.yml@templates
    parameters:
      appName: 'sample-spring-kotlin-microservice'
  - template: deploy-argocd-stage.yml@templates
    parameters:
      appName: 'sample-spring-kotlin-microservice'
```

xxx

```shell
oc process build-config-template -p APP_NAME=web-api-2 -p CONTEXT_DIR=src/WebApi.App -p IMAGE_TAG=1.1 | oc apply -f -
oc start-build web-api-2 --wait=true --follow=true
```


```yaml
resources:
  repositories:
    - repository: templates
      type: git
      name: pipeline-templates
      ref: refs/heads/main
    - repository: appRepo
      type: git
      name: sample-app1
      ref: main

pool:
  name: ci-cd

variables:
  - group: Quay
  - group: ACS
  - group: argocd-test
  - group: argocd-staging
  - group: argocd-uat
  - group: argocd-prod
  
stages:
  - template: dotnet-build-stage-remote.yml@templates
    parameters:
      moduleName: 'WebApi.App'
      projectPath: 'src/WebApi.App/WebApi.App.csproj'
      testProjectPath: 'tests/WebApi.App.Tests/WebApi.App.Tests.csproj'
      sonarProjectKey: RHOS-web-api-app
      feedName: RedHat-openshift-dr/frontex
  - template: build-image-stage-remote.yml@templates
    parameters:
      rootPath: 'src/WebApi.App'
      projectPath: 'src/WebApi.App/WebApi.App.csproj'
      appName: webapi-app
  - template: deploy-argocd-stage-remote.yml@templates
    parameters:
      appName: sample-app
      moduleName: webapi-app
  - template: deploy-argocd-stage-remote.yml@templates
    parameters:
      appName: sample-app
      moduleName: webapi-app
      env: staging
  - template: deploy-argocd-stage-remote.yml@templates
    parameters:
      appName: sample-app
      moduleName: webapi-app
      env: uat
  - template: deploy-argocd-stage-remote.yml@templates
    parameters:
      appName: sample-app
      moduleName: webapi-app
      env: prod
```