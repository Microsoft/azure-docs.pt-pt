---
title: 'Azure CLI: Monitor& base de dados de escala na Base de Dados Azure SQL'
description: Script de exemplo Azure CLI para monitorizar e escalar uma única base de dados na Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: be57309e4b327027ed0185c8eabf783a18cc957e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053724"
---
# <a name="use-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Utilize o CLI para monitorizar e escalar uma única base de dados na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script Azure CLI escala uma única base de dados na Base de Dados Azure SQL para um tamanho de computação diferente após consultar a informação de tamanho da base de dados.

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a single database in Azure SQL Database")]

> [!TIP]
> Utilize a lista de [op az sql db](/cli/azure/sql/db/op?#az-sql-db-op-list) op para obter uma lista de operações realizadas na base de dados e [az sql db op cancele](/cli/azure/sql/db/op#az-sql-db-op-cancel) para cancelar uma operação de atualização na base de dados.

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | Comandos do servidor. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Mostra as informações de utilização do tamanho para uma única base de dados ou agrinada. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Amostras adicionais de script CLI podem ser encontradas em scripts de [amostra sinuoso Azure CLI](../az-cli-script-samples-content-guide.md).
