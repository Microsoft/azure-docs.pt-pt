---
title: Implementação contínua com Azure DevOps (Pré-visualização)
titleSuffix: Azure Cognitive Services
description: Neste artigo, aprende a configurar a implementação contínua para as suas aplicações de Comandos Personalizados. Cria-se os scripts para suportar os fluxos de trabalho de implementação contínua.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927605"
---
# <a name="continuous-deployment-with-azure-devops"></a>Implementação Contínua com o Azure DevOps

Neste artigo, aprende a configurar a implementação contínua para as suas aplicações de Comandos Personalizados. Os scripts para suportar o fluxo de trabalho ci/CD são fornecidos a si.

## <a name="prerequisite"></a>Pré-requisito
> [!div class = "checklist"]
> * Uma aplicação de comandos personalizados para desenvolvimento (DEV)
> * Uma aplicação de Comandos Personalizados para produção (PROD)
> * Inscreva-se nos [Pipelines Azure](/azure/devops/pipelines/get-started/pipelines-sign-up)

## <a name="exportimportpublish"></a>Exportação/Importação/Publicação

Os scripts são hospedados no [Cognitive Services Voice Assistant - Comandos Personalizados](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Clone os guiões no diretório de bash para o seu repositório. Certifique-se de manter o mesmo caminho.

### <a name="set-up-a-pipeline"></a>Criar um oleoduto 

1. Vá ao **Azure DevOps - Pipelines** e clique em "New Pipeline"
1. Na secção **Connect,** selecione a localização do seu repositório onde estes scripts estão localizados
1. Na secção **Select,** selecione o seu repositório
1. Na secção **Configure,** selecione "Starter pipeline"
1. Em seguida, você terá um editor com um ficheiro YAML, substituirá a secção "passos" por este script.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Note que estes scripts assumem que você está usando a região `westus2` , se não for esse o caso atualizar os argumentos das tarefas em conformidade

    > [!div class="mx-imgBorder"]
    > ![Screenshot que destaca o valor da região nos argumentos.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. No botão "Guardar e executar", abra o dropdown e clique em "Guardar"

### <a name="hook-up-the-pipeline-with-your-application"></a>Ligue o oleoduto com a sua aplicação

1. Navegue para a página principal do oleoduto.
1. No canto superior direito, selecione **o gasoduto Editar**. Leva-te a um editor da YAML. 
1. No canto superior direito ao lado do botão "Executar", selecione **Variáveis**. Clique **em Nova variável**.
1. Adicione estas variáveis:
    
    | Variável | Descrição |
    | ------- | --------------- | ----------- |
    | FonteAppId | ID da aplicação DEV |
    | TargetAppId | ID da aplicação PROD |
    | AssinaturaKey | Chave de subscrição utilizada para ambas as aplicações |
    | Cultura | Cultura das candidaturas (isto é, en-us) |

    > [!div class="mx-imgBorder"]
    > ![Enviar carga útil da atividade](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Clique em "Executar" e, em seguida, clique na execução "Job". 

    Você deve ver uma lista de tarefas em execução que contém: "App de origem de exportação", "Import to target app" & "Train and Publish target app"

## <a name="deploy-from-source-code"></a>Implantação a partir do código fonte

Caso pretenda manter a definição da sua aplicação num repositório, fornecemos os scripts para implementações a partir do código fonte. Uma vez que os scripts estão em bash, se estiver a utilizar o Windows terá de instalar o [subsistema Linux](/windows/wsl/install-win10).

Os scripts são hospedados no [Cognitive Services Voice Assistant - Comandos Personalizados](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Clone os guiões no diretório de bash para o seu repositório. Certifique-se de manter o mesmo caminho.

### <a name="prepare-your-repository"></a>Prepare o seu repositório

1. Crie um diretório para a sua aplicação, no nosso exemplo crie uma chamada "apps".
1. Atualize os argumentos do roteiro de bash abaixo, e corra. Importará o modelo de diálogo da sua aplicação para o ficheiro myapp.js
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argumentos | Description |
    | ------- | --------------- | ----------- |
    | region | região da aplicação, ou seja, westus2. |
    | subscrição | chave de subscrição do seu recurso de fala. |
    | appid | o ID de aplicação dos Comandos Personalizados que pretende exportar. |

1. Empurre estas alterações para o seu repositório.

### <a name="set-up-a-pipeline"></a>Criar um oleoduto 

1. Vá ao **Azure DevOps - Pipelines** e clique em "New Pipeline"
1. Na secção **Connect,** selecione a localização do seu repositório onde estes scripts estão localizados
1. Na secção **Select,** selecione o seu repositório
1. Na secção **Configure,** selecione "Starter pipeline"
1. Em seguida, você terá um editor com um ficheiro YAML, substituirá a secção "passos" por este script.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > estes scripts assumem que você está usando a região westus2, se não for esse o caso atualizar os argumentos das tarefas em conformidade

1. No botão "Guardar e executar", abra o dropdown e clique em "Guardar"

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>Ligue o oleoduto com as aplicações-alvo

1. Navegue para a página principal do oleoduto.
1. No canto superior direito, selecione **o gasoduto Editar**. Leva-te a um editor da YAML. 
1. No canto superior direito ao lado do botão "Executar", selecione **Variáveis**. Clique **em Nova variável**.
1. Adicione estas variáveis:

    | Variável | Descrição |
    | ------- | --------------- | ----------- |
    | TargetAppId | ID da aplicação PROD |
    | AssinaturaKey | Chave de subscrição utilizada para ambas as aplicações |
    | Cultura | Cultura das candidaturas (isto é, en-us) |

1. Clique em "Executar" e, em seguida, clique na execução "Job".
    Você deve ver uma lista de tarefas em execução que contém: "App importe" & "Train and Publish app"

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ver amostras no GitHub](https://aka.ms/speech/cc-samples)
