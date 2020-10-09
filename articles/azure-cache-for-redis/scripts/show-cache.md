---
title: Obtenha detalhes de um Azure Cache para Redis - Azure CLI
description: Esta amostra de código Azure CLI mostra como recuperar os detalhes de uma Cache Azure para a instância Redis, incluindo o seu estado de provisionamento.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8874a17b40b60a194811630b2f93d2be9d7faf7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494711"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Obtenha detalhes de um Azure Cache para Redis

Neste cenário, aprende-se a recuperar os detalhes de uma Cache Azure para a instância Redis, incluindo o seu estatuto de provisionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para recuperar os detalhes de uma Cache Azure para a instância Redis. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Recupere detalhes de uma Cache Azure para o exemplo de Redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Cache Azure adicional para amostras de scripts CLI Redis pode ser encontrado na [Cache Azure para documentação Redis](../cli-samples.md).
