---
title: ClI exemplo-monitor-escala única Base de dados Azure SQL
description: Script de exemplo da CLI do Azure para monitorizar e dimensionar uma base de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 191de1fdbbee3e31bfcd366cbec8a70732b23b5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061822"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Utilizar a CLI para monitorizar e dimensionar uma base de dados SQL

Este exemplo de script da CLI do Azure dimensiona uma base de dados SQL do Azure para um tamanho da computação diferente depois de consultar as informações de tamanho da base de dados.

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

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

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../sql-database-cli-samples.md).
