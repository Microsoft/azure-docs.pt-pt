---
title: 'Azure CLI: Mover base de dados entre piscinas elásticas'
description: Script de exemplo Azure CLI que cria duas piscinas elásticas e move uma base de dados em Base de Dados SQL de uma piscina elástica para outra usando o Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 2bcf9f4218f535556ff83ccaad130bb1da28ef69
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054060"
---
# <a name="use-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>Use o CLI para mover uma base de dados na Base de Dados SQL numa piscina elástica SQL


Este exemplo de script Azure CLI cria duas piscinas elásticas e move uma base de dados em Base de Dados SQL de uma piscina elástica SQL para outra piscina elástica SQL, e depois move a base de dados de piscina elástica para um único tamanho de computação de base de dados.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

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
| [az sql elástico-piscinas](/cli/azure/sql/elastic-pool) | Comandos de piscina elástica. |
| [az sql db](/cli/azure/sql/db) | Comandos de base de dados. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../az-cli-script-samples-content-guide.md).
