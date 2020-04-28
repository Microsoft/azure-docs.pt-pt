---
title: CI/CD para Nuvem de primavera Azure
description: CI/CD para Nuvem de primavera Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278519"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD para Nuvem de primavera Azure

A integração contínua e as ferramentas de entrega contínua supor em rápido aos desenvolvedores a implementação rápida de atualizações para aplicações existentes com o mínimo de esforço e risco. A Azure DevOps ajuda-o a organizar e a controlar estes trabalhos-chave. Atualmente, a Azure Spring Cloud não oferece um plugin Azure DevOps específico.  No entanto, pode integrar as suas aplicações Spring Cloud com DevOps utilizando uma [tarefa Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). Este artigo irá mostrar-lhe como usar uma tarefa Azure CLI com a Azure Spring Cloud para integrar com o Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Criar uma ligação de serviço do Gestor de Recursos Azure

Leia [este artigo](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) para aprender a criar uma ligação de serviço do Gestor de Recursos Azure ao seu projeto Azure DevOps. Certifique-se de selecionar a mesma subscrição que está a utilizar para a sua instância de serviço Azure Spring Cloud.

## <a name="azure-cli-task-templates"></a>Modelos de tarefa sintetizar

### <a name="deploy-artifacts"></a>Implementar artefactos

Pode construir e implementar os seus `tasks`projetos usando uma série de . Este corte define primeiro uma tarefa Maven para construir a aplicação, seguida de uma segunda tarefa que implementa o ficheiro JAR utilizando a extensão Azure Spring Cloud Azure CLI.

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

É possível implantar diretamente em Azure sem um passo de construção separado.

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
