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