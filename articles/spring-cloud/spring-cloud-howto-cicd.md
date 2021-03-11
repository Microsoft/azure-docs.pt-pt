---
title: CI/CD para Azure Spring Cloud
description: CI/CD para Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 331ef39facb9f7cf8f069f2a238be325f53de2d0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618038"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD para Azure Spring Cloud

A integração contínua e as ferramentas de entrega contínua permitem-lhe implementar rapidamente atualizações para aplicações existentes com o mínimo de esforço e risco. A Azure DevOps ajuda-o a organizar e controlar estes trabalhos-chave. Atualmente, a Azure Spring Cloud não oferece um plugin Azure DevOps específico.  No entanto, pode integrar as suas aplicações Spring Cloud com DevOps utilizando uma [tarefa Azure CLI](/azure/devops/pipelines/tasks/deploy/azure-cli).

Este artigo mostra-lhe como usar uma tarefa Azure CLI com Azure Spring Cloud para integrar com Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Criar uma ligação de serviço Azure Resource Manager

Leia [este artigo](/azure/devops/pipelines/library/connect-to-azure) para aprender a criar uma ligação de serviço Azure Resource Manager ao seu projeto Azure DevOps. Certifique-se de selecionar a mesma subscrição que está a usar para a sua instância de serviço Azure Spring Cloud.

## <a name="azure-cli-task-templates"></a>Modelos de tarefa Azure CLI
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Implantar artefactos

Pode construir e implementar os seus projetos utilizando uma série de `tasks` . Este snippet define variáveis, uma tarefa .NET Core para construir a aplicação, e uma tarefa do Azure CLI para implementar o ficheiro *.zip.*

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --assign-endpoint
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Implantar artefactos

Pode construir e implementar os seus projetos utilizando uma série de `tasks` . Este snippet define primeiro uma tarefa Maven para construir a aplicação, seguida de uma segunda tarefa que implementa o ficheiro JAR usando a extensão Azure Spring Cloud CLI.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Implantação a partir da fonte

É possível deslocar-se diretamente para Azure sem um passo de construção separado.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md)