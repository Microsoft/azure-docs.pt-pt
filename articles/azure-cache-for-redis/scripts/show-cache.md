---
title: Obter detalhes de um cache do Azure para Redis-CLI do Azure
description: Este exemplo de código CLI do Azure mostra como recuperar os detalhes de um cache do Azure para a instância Redis, incluindo seu status de provisionamento.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 54920017f4a0598a74a54114e3d1e0a5392d7d2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411062"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Obter os detalhes de uma Cache do Azure para Redis

Neste cenário, irá aprender a obter os detalhes de um Cache do Azure para a instância de Redis, incluindo o estado de aprovisionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para obter os detalhes de um Cache do Azure para a instância de Redis. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [show do AZ redis](https://docs.microsoft.com/cli/azure/redis) | Obter os detalhes de uma Cache do Azure para a instância de Redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

A Cache do Azure adicionais para exemplos do script da CLI de Redis pode ser encontrada na [Cache do Azure para a documentação do Redis](../cli-samples.md).
