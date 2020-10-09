---
title: Apagar uma cache Azure para Redis - Azure CLI
description: Esta amostra de código Azure CLI mostra como eliminar uma cache Azure para a instância Redis utilizando o comando az redis delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: b1e044c6f61e51de1d8f2ba07f64918307109e46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494728"
---
# <a name="delete-an-azure-cache-for-redis"></a>Apagar uma cache Azure para Redis

Neste cenário, aprende-se a apagar um Cache Azure para Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para eliminar um Cache Azure para a instância Redis. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az redis apagar](https://docs.microsoft.com/cli/azure/redis) | Apague a cache de Azure para a instância redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Cache Azure adicional para amostras de scripts CLI Redis pode ser encontrado na [Cache Azure para documentação Redis](../cli-samples.md).
