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
ms.openlocfilehash: 9ce6e20e5e4866bc4daa3f331fa2a8612ac6c260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184166"
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
| [az redis apagar](/cli/azure/redis) | Apague a cache de Azure para a instância redis. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Cache Azure adicional para amostras de scripts CLI Redis pode ser encontrado na [Cache Azure para documentação Redis](../cli-samples.md).