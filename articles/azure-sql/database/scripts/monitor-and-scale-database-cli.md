---
title: 'Azure CLI: Monitorar e escalar uma única base de dados na Base de Dados Azure SQL'
description: Utilize um script de exemplo Azure CLI para monitorizar e escalar uma única base de dados na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: bd982ab6dc66674e705f080511282bcfeb909872
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787184"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Utilize o CLI Azure para monitorizar e escalar uma única base de dados na Base de Dados Azure SQL

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script Azure CLI escala uma única base de dados na Base de Dados Azure SQL para um tamanho de computação diferente depois de consultar a informação de tamanho da base de dados.

Se optar por instalar e utilizar o Azure CLI localmente, este artigo requer que esteja a executar a versão 2.0 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Use [a lista de db op az sql](/cli/azure/sql/db/op?#az_sql_db_op_list) para obter uma lista de operações realizadas na base de dados, e use [az sql db op cancelar](/cli/azure/sql/db/op#az_sql_db_op_cancel) para cancelar uma operação de atualização na base de dados.

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Script | Description |
|---|---|
| [az sql server](/cli/azure/sql/server) | Comandos do servidor. |
| [az sql db show-usage](/cli/azure/sql#az_sql_show_usage) | Mostra as informações de utilização de tamanho de uma base de dados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Amostras adicionais de scripts CLI podem ser encontradas em [scripts de amostra do Azure CLI](../az-cli-script-samples-content-guide.md).
