---
title: ClI exemplo-backup-Azure SQL base de dados
description: Roteiro de exemplo Azure CLI para fazer backup de uma base de dados única Azure SQL para um recipiente de armazenamento Azure
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: 68af42bd83565243dad5d48f227fa5cef3115cf6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061869"
---
# <a name="use-cli-to-backup-an-azure-sql-single-database-to-an-azure-storage-container"></a>Utilize o CLI para fazer backup de uma base de dados única Azure SQL para um recipiente de armazenamento Azure

Este exemplo Azure CLI apoia uma base de dados Azure SQL para um recipiente de armazenamento Azure.  

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/backup-database/backup-database.sh "Restore SQL Database")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az sql server](/cli/azure/sql/server) | Comandos do servidor. |
| [az sql db](/cli/azure/sql/db) | Comandos de base de dados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../sql-database-cli-samples.md).
