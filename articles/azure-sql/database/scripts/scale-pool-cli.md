---
title: 'Az CL: Escala uma piscina elástica'
description: Roteiro de exemplo Azure CLI para escalar uma piscina elástica na Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 04736fbfeef9cf0584cf7c1f1a5daf91813ecbc7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053946"
---
# <a name="use-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Use o CLI para escalar uma piscina elástica na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script Azure CLI cria piscinas elásticas na Base de Dados Azure SQL, move bases de dados em conjunto e altera tamanhos de computação de piscina elástica.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

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
| [az sql db](/cli/azure/sql/db) | Comandos de base de dados. |
| [az sql elástico-piscinas](/cli/azure/sql/elastic-pool) | Comandos de piscina elástica. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../az-cli-script-samples-content-guide.md).
