---
title: 'CLI: conectar um aplicativo ao Redis'
description: Saiba como usar o CLI do Azure para automatizar a implantação e o gerenciamento do seu aplicativo do serviço de aplicativo. Este exemplo mostra como conectar um aplicativo a um cache do Azure para Redis.
author: msangapu-msft
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: a5654ea8c0333e21421e0f9c55cc00d70a7be567
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688437"
---
# <a name="connect-an-app-service-app-to-an-azure-cache-for-redis-using-cli"></a>Conectar um aplicativo do serviço de aplicativo a um cache do Azure para Redis usando a CLI

Este script de exemplo cria um cache do Azure para Redis e um aplicativo do serviço de aplicativo. Em seguida, ele vincula o cache do Azure para Redis ao aplicativo usando as configurações do aplicativo.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Se optar por instalar e usar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Esse script usa os seguintes comandos para criar um grupo de recursos, um aplicativo do serviço de aplicativo, um cache do Azure para Redis e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Cria um aplicativo do serviço de aplicativo. |
| [`az redis create`](/cli/azure/redis?view=azure-cli-latest#az-redis-create) | Crie um novo cache do Azure para a instância Redis. |
| [`az redis list-keys`](/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys) | Lista as chaves de acesso para o cache do Azure para a instância Redis. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | Cria ou atualiza uma configuração de aplicativo para um aplicativo do serviço de aplicativo. As definições da aplicação são expostas como variáveis de ambiente para a sua aplicação. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
