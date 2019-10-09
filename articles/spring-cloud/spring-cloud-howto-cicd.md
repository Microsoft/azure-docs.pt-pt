---
title: CI/CD para o Azure Spring Cloud
description: CI/CD para o Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 96795315fcb0dd9b90cd55e8baa46c6a394882ce
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038837"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD para o Azure Spring Cloud

As ferramentas de integração contínua e entrega contínua permitem que os desenvolvedores implantem atualizações rapidamente em aplicativos existentes com esforço e risco mínimos. O Azure DevOps ajuda a organizar e controlar esses trabalhos importantes. Atualmente, o Azure Spring Cloud não oferece um plug-in do Azure DevOps específico.  No entanto, você pode integrar seus aplicativos Spring Cloud ao DevOps usando uma [tarefa CLI do Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). Este artigo mostrará como usar uma tarefa de CLI do Azure com o Azure Spring Cloud para integrar com o Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Criar uma conexão de serviço Azure Resource Manager

Leia [Este artigo](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) para saber como criar uma conexão de serviço Azure Resource Manager para seu projeto DevOps do Azure. Certifique-se de selecionar a mesma assinatura que você está usando para sua instância do serviço de nuvem do Azure Spring.

## <a name="azure-cli-task-templates"></a>Modelos de tarefa CLI do Azure

### <a name="deploy-artifacts"></a>Implantar artefatos

Você pode criar e implantar seus projetos usando uma série de `tasks`. Esse trecho de código define primeiro uma tarefa Maven para compilar o aplicativo, seguido por uma segunda tarefa que implanta o arquivo JAR usando a extensão de CLI do Azure de nuvem do Azure Spring.

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
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Implantar da origem

É possível implantar diretamente no Azure sem uma etapa de compilação separada.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
