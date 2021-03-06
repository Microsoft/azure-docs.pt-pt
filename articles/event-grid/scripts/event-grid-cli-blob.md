---
title: Exemplo de script da CLI do Azure - Subscrever uma conta de armazenamento de Blobs | Microsoft Docs
description: Este artigo fornece uma amostra do script Azure CLI que mostra como subscrever eventos para uma conta de Armazenamento Azure Blob.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1bc1f67277741dfa12209451375301cec7a63e9f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871435"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Subscrever eventos para uma conta de armazenamento de Blobs com a CLI do Azure

Este script cria uma subscrição do Event Grid para os eventos para uma conta de armazenamento de Blobs. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para criar a subscrição de eventos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Crie uma subscrição do Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - versão de extensão | Crie uma subscrição do Event Grid. |

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a consulta de subscrições, veja [Consultar subscrições do Event Grid](../query-event-subscriptions.md).
* Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).
