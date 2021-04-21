---
title: 'Azure CLI: Restaurar uma cópia de segurança'
description: Azure CLI exemplo script para restaurar uma base de dados em Azure SQL Database para um ponto mais cedo no tempo a partir de backups automáticos.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: f4054d19ccf4d136e30896bc066e51c92a4a4ad9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783472"
---
# <a name="use-cli-to-restore-a-single-database-in-azure-sql-database-to-an-earlier-point-in-time"></a>Utilize o CLI para restaurar uma única base de dados na Base de Dados Azure SQL para um ponto anterior no tempo

Este exemplo do Azure CLI restaura uma única base de dados na Base de Dados Azure SQL a um ponto específico no tempo.  

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/restore-database/restore-database.sh "Restore SQL Database")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Descrição |
|---|---|
| [az sql db restaurar](/cli/azure/sql/db#az_sql_db_restore) | Restaurar o comando da base de dados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
