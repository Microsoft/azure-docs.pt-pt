---
title: Azure CLI - subscreva o grupo de recursos & filtro por recurso
description: Este artigo fornece uma amostra de script Azure CLI que mostra como subscrever eventos de Grade de Eventos para um recurso e filtro para um recurso.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 11935866f37dfaae15f25dac6deeace4e89547fc
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171300"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>Subscrever eventos de um grupo de recursos e filtrar um recurso com a CLI

Este script cria uma subscrição do Event Grid para os eventos para um grupo de recursos. Utiliza um filtro para obter eventos apenas de um recurso especificado no grupo de recursos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

O script de exemplo de pré-visualização exige a extensão do Event Grid. Para instalar, execute `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Script de exemplo - estável

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events/filter-events.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Script de exemplo - extensão de pré-visualização

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events-preview/filter-events-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para criar a subscrição de eventos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Crie uma subscrição do Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - versão de extensão | Crie uma subscrição do Event Grid. |

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a consulta de subscrições, veja [Consultar subscrições do Event Grid](../query-event-subscriptions.md).
* Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
