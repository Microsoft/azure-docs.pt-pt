---
title: Criar, ver e gerir tópicos do sistema Azure Event Grid usando O CLI
description: Este artigo mostra como usar o CLI Azure para criar, visualizar e eliminar tópicos do sistema.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 354afb89b145e288f525e40ad700e8f8a67c6dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115048"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Criar, ver e gerir tópicos do sistema de grade de eventos usando O Azure CLI
Este artigo mostra-lhe como criar e gerir tópicos do sistema usando o Azure CLI. Para uma visão geral dos tópicos do sistema, consulte [os tópicos do Sistema](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Extensão de instalação para Azure CLI
Para o Azure CLI, você precisa da [extensão da Grade de Eventos](/cli/azure/azure-cli-extensions-list).

Em Cloud Shell:

- Se já instalou a extensão anteriormente, atualize-a: `az extension update -n eventgrid`
- Se não tiver instalado a extensão anteriormente, instale-a:  `az extension add -n eventgrid`

Para uma instalação local:

1. [Instale o Azure CLI](/cli/azure/install-azure-cli). Certifique-se de que tem a versão mais recente, verificando com `az --version` .
2. Desinstalar versões anteriores da extensão: `az extension remove -n eventgrid`
3. Instale a extensão eventgrid com `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Criar um tópico de sistema

- Para criar primeiro um tópico de sistema sobre uma fonte Azure e, em seguida, criar uma subscrição de eventos para esse tópico, consulte os seguintes tópicos de referência:
    - [az eventgrid sistema-tópico criar](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        Para uma lista de `topic-type` valores que pode utilizar para criar um tópico de sistema, execute o seguinte comando. Estes valores de tipo tópico representam as fontes de evento que suportam a criação de tópicos do sistema. Por favor, ignore `Microsoft.EventGrid.Topics` e `Microsoft.EventGrid.Domains` da lista. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid sistema-tópico-subscrição de eventos criar](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Para criar um tópico de sistema (implicitamente) ao criar uma subscrição de eventos para uma fonte Azure, utilize o método [de criação de subscrição de eventos az.](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) Eis um exemplo:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Para obter um tutorial com instruções passo a passo, consulte [a conta de armazenamento de Subscreva.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)

## <a name="view-all-system-topics"></a>Ver todos os tópicos do sistema
Para ver todos os tópicos e detalhes do sistema de um tópico de sistema selecionado, utilize os seguintes comandos:

- [az eventgrid lista de tópicos do sistema](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid sistema-tópico show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Eliminar um tópico de sistema
Para eliminar um tópico do sistema, utilize o seguinte comando: 

- [az eventgrid sistema-tópico eliminar](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Passos seguintes
Consulte os tópicos do Sistema na secção [Azure Event Grid](system-topics.md) para saber mais sobre tópicos do sistema e tipos de tópicos suportados pela Azure Event Grid. 