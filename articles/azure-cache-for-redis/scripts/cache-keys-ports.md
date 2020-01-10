---
title: Obter o nome do host, portas, chaves-cache do Azure para Redis-CLI do Azure
description: Este exemplo de código CLI do Azure mostra como obter o nome do host, as portas e as chaves para um cache do Azure para a instância Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411307"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Obter o nome de anfitrião, portas e as chaves para a Cache do Azure para Redis

Neste cenário, irá aprender a obter o nome de anfitrião, portas e as chaves utilizadas para ligar a uma Cache do Azure para a instância de Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para obter o nome de anfitrião, as chaves e as portas de uma Cache do Azure para a instância de Redis. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [show do AZ redis](https://docs.microsoft.com/cli/azure/redis) | Obter os detalhes de uma Cache do Azure para a instância de Redis. |
| [lista de redis de AZ-chaves](https://docs.microsoft.com/cli/azure/redis) | Obter chaves de acesso para uma Cache do Azure para a instância de Redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

A Cache do Azure adicionais para exemplos do script da CLI de Redis pode ser encontrada na [Cache do Azure para a documentação do Redis](../cli-samples.md).
