---
title: Obtenha o nome de anfitrião, portas, chaves - Azure Cache para Redis - Azure CLI
description: Esta amostra de código Azure CLI mostra como obter o nome de anfitrião, portas e chaves para um Azure Cache para redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411307"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Obtenha o nome de anfitrião, portas e chaves para Azure Cache para Redis

Neste cenário, você aprende como recuperar o nome de anfitrião, portas e chaves usadas para ligar a um Azure Cache para redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para recuperar o nome de anfitrião, chaves e portas de um Azure Cache para a instância Redis. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Recupere detalhes de um Cache Azure para o caso Redis. |
| [az redis lista-chaves](https://docs.microsoft.com/cli/azure/redis) | Recupere as chaves de acesso para um Cache Azure para a instância Redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

As amostras de script sinuosas do Redis CLI podem ser encontradas no [Azure Cache para documentação redis](../cli-samples.md).
