---
title: Criar, ver e gerir tópicos do sistema Azure Event Grid usando O CLI
description: Este artigo mostra como usar o CLI Azure para criar, visualizar e eliminar tópicos do sistema.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 19a22a0a3b528a9a72fdd51c589e42bf2fba5ce7
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669941"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Criar, ver e gerir tópicos do sistema de grade de eventos usando O Azure CLI
Este artigo mostra-lhe como criar e gerir tópicos do sistema usando o Azure CLI. Para uma visão geral dos tópicos do sistema, consulte [os tópicos do Sistema](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Extensão de instalação para Azure CLI
Para o Azure CLI, você precisa da [extensão da Grade de Eventos](/cli/azure/azure-cli-extensions-list).

Em CloudShell:

- Se já instalou a extensão anteriormente, atualize-a:`az extension update -n eventgrid`
- Se não tiver instalado a extensão anteriormente, instale-a:`az extension add -n eventgrid`

Para uma instalação local:

1. [Instale o Azure CLI](/cli/azure/install-azure-cli). Certifique-se de que tem a versão mais recente, verificando com a versão az.
2. Desinstalar versões anteriores da extensão:`az extension remove -n eventgrid`
3. Instale a extensão eventgrid com`az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Criar um tópico de sistema

- Para criar primeiro um tópico de sistema sobre uma fonte Azure e, em seguida, criar uma subscrição de eventos para esse tópico, consulte os seguintes tópicos de referência:
    - [az eventgrid sistema-tópico criar](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli
        az eventgrid system-topic create \
            -g myResourceGroup \
            --name systemtopic1 \
            --location westus2 \
            --topic-type microsoft.storage.storageaccounts \
            --source /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/storagegaccountname
        ```
    - [az eventgrid sistema-tópico-subscrição de eventos criar](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli
        az eventgrid system-topic event-subscription create --name es1 \
            -g rg1 --system-topic-name systemtopic1 \
            --endpoint https://contoso.azurewebsites.net/api/f1?code=code         
          ```
- To create a system topic (implicitly) when creating an event subscription for an Azure source, use the [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) method. Here's an example:
    
    ```azurecli
    storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
    endpoint=https://$sitename.azurewebsites.net/api/updates

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <event_subscription_name> \
      --endpoint $endpoint
    ```
    Para obter um tutorial com instruções passo a passo, consulte [a conta de armazenamento de Subscreva.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)

## <a name="view-all-system-topics"></a>Ver todos os tópicos do sistema
Para ver todos os tópicos e detalhes do sistema de um tópico de sistema selecionado, utilize os seguintes comandos:

- [az eventgrid lista de tópicos do sistema](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli
    az eventgrid system-topic list   
     ```
- [az eventgrid sistema-tópico show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli
    az eventgrid system-topic show -g rg1 -n systemtopic1    
     ```

## <a name="delete-a-system-topic"></a>Eliminar um tópico de sistema
Para eliminar um tópico do sistema, utilize o seguinte comando: 

- [az eventgrid sistema-tópico eliminar](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli
    az eventgrid system-topic delete -g myResourceGroup --name systemtopic1  
     ```

## <a name="next-steps"></a>Próximos passos
Consulte os tópicos do Sistema na secção [Azure Event Grid](system-topics.md) para saber mais sobre tópicos do sistema e tipos de tópicos suportados pela Azure Event Grid. 
