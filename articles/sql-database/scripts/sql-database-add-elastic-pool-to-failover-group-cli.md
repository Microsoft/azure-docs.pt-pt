---
title: Exemplo CLI - Grupo Failover - Piscina elástica Azure SQL Database
description: Script de exemplo Azure CLI para criar um conjunto elástico Azure SQL Database, adicioná-lo a um grupo failover, e testar falha.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 42cc10398fce831b6343f69df03104d428700ac3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022782"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Use o CLI para adicionar uma piscina elástica Azure SQL Database a um grupo de failover

Este exemplo de script Azure CLI cria uma única base de dados, adiciona-o a uma piscina elástica, cria um grupo de failover, e testa falha.

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| | |
|---|---|
| [az sql elástico-piscina](/cli/azure/sql/elastic-pool) | Comandos de piscina elástica. |
| [az sql failover-grupo](/cli/azure/sql/failover-group) | Falha de comandos de grupo. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure/overview).

Amostras adicionais de script sQL Database Azure CLI podem ser encontradas nos [scripts Azure CLI](../../azure-sql/database/az-cli-script-samples-content-guide.md)da Base de Dados Azure Azure .
