---
title: 'O Azure CLI: Adicione uma base de dados a um grupo de failover'
description: Utilize o script de exemplo do Azure CLI para criar uma base de dados na Base de Dados Azure SQL, adicione-a a um grupo de falha automática e teste falha.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 09c98f626d9f32a2a6f9f3e31e0db0e4751adacb
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196982"
---
# <a name="use-the-azure-cli-to-add-a-database-to-a-failover-group"></a>Utilize o CLI Azure para adicionar uma base de dados a um grupo de failover

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script do Azure CLI cria uma base de dados na Base de Dados Azure SQL, cria um grupo de failover, adiciona a base de dados e testa o failover.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add Azure SQL Database to failover group")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | Comandos de base de dados. |
| [az sql failover grupo](/cli/azure/sql/failover-group) | Comandos de grupo de failover. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../az-cli-script-samples-content-guide.md).
