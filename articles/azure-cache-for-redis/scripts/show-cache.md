---
title: Obtenha detalhes de um Azure Cache para Redis - Azure CLI
description: Esta amostra de código Azure CLI mostra como recuperar os detalhes de um Cache Azure para a redis, incluindo o seu estado de provisionamento.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 54920017f4a0598a74a54114e3d1e0a5392d7d2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411062"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Obtenha detalhes de um Cache Azure para Redis

Neste cenário, aprende-se a recuperar os detalhes de um Azure Cache para redis, incluindo o seu estado de provisionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Explicação do script

Este script usa os seguintes comandos para recuperar os detalhes de um Cache Azure para a instância Redis. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Recupere detalhes de um Cache Azure para o caso Redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

As amostras de script sinuosas do Redis CLI podem ser encontradas no [Azure Cache para documentação redis](../cli-samples.md).
