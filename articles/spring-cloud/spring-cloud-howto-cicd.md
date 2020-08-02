---
title: CI/CD para Azure Spring Cloud
description: CI/CD para Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d31be45f5cf608b7dfd727741de68544574d7f8a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496462"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD para Azure Spring Cloud

A integração contínua e as ferramentas de entrega contínua permitem aos desenvolvedores implementar rapidamente atualizações para aplicações existentes com o mínimo de esforço e risco. A Azure DevOps ajuda-o a organizar e controlar estes trabalhos-chave. Atualmente, a Azure Spring Cloud não oferece um plugin Azure DevOps específico.  No entanto, pode integrar as suas aplicações Spring Cloud com DevOps utilizando uma [tarefa Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). Este artigo irá mostrar-lhe como usar uma tarefa Azure CLI com Azure Spring Cloud para integrar com Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Criar uma ligação de serviço Azure Resource Manager

Leia [este artigo](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) para aprender a criar uma ligação de serviço Azure Resource Manager ao seu projeto Azure DevOps. Certifique-se de selecionar a mesma subscrição que está a usar para a sua instância de serviço Azure Spring Cloud.

## <a name="azure-cli-task-templates"></a>Modelos de tarefa Azure CLI

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
