---
title: Criar um cache do Azure Premium para Redis com clustering-CLI do Azure
description: Este exemplo de código CLI do Azure mostra como criar um cache do Azure da camada Premium de 6 GB para Redis com clustering habilitado e dois fragmentos.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411150"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Criar uma Cache do Azure Premium para Redis com clustering

Neste cenário, saiba como criar um Cache do Azure o escalão Premium 6 GB para Redis com o clustering ativado e partições horizontais de duas.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos e ativar um escalão Premium a Cache de Redis do Azure com o clustering. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Criar AZ redis](https://docs.microsoft.com/cli/azure/redis) | Crie a Cache do Azure para a instância de Redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

A Cache do Azure adicionais para exemplos do script da CLI de Redis pode ser encontrada na [Cache do Azure para a documentação do Redis](../cli-samples.md).
