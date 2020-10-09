---
title: Obtenha o nome de anfitrião, portas, chaves - Azure Cache para Redis - Azure CLI
description: Esta amostra de código Azure CLI mostra como obter o nome de hospedeiro, portas e chaves para um Cache Azure para a instância Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9fef834e550f144075ec9cc72cac3b11cc1e99e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87504216"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Obtenha o nome de anfitrião, portas e chaves para Azure Cache para Redis

Neste cenário, você aprende a recuperar o nome de hospedeiro, portas e teclas usadas para ligar a um Cache Azure para o exemplo de Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para recuperar o nome de anfitrião, chaves e portas de uma Cache Azure para a instância Redis. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Recupere detalhes de uma Cache Azure para o exemplo de Redis. |
| [az redis lista-chaves](https://docs.microsoft.com/cli/azure/redis) | Recupere as chaves de acesso para uma Cache Azure para a instância Redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Cache Azure adicional para amostras de scripts CLI Redis pode ser encontrado na [Cache Azure para documentação Redis](../cli-samples.md).
