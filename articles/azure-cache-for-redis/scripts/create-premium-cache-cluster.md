---
title: Crie um Cache Azure Premium para Redis com clustering - Azure CLI
description: Esta amostra de código Azure CLI mostra como criar um Azure Cache de 6 GB premium para Redis com clustering habilitado e dois fragmentos.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411150"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Crie um Cache Azure Premium para Redis com clustering

Neste cenário, aprende-se a criar um Azure Cache de 6 GB premium para Redis com clustering habilitado e dois fragmentos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos e um Azure Cache de nível Premium para Redis com suporte de agrupamento. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az redis criar](https://docs.microsoft.com/cli/azure/redis) | Crie cache azure para a instância Redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

As amostras de script sinuosas do Redis CLI podem ser encontradas no [Azure Cache para documentação redis](../cli-samples.md).
