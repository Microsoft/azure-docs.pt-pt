---
title: 'O Azure CLI: Criar uma única base de dados'
description: Utilize este script de exemplo Azure CLI para criar uma única base de dados.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: dc17ab55cb473036d8c40bd1e533bcc20c4c6259
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92742252"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Utilize o CLI Azure para criar uma única base de dados e configurar uma regra de firewall

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script do Azure CLI cria uma única base de dados na Base de Dados Azure SQL e configura uma regra de firewall ao nível do servidor. Depois de o script ter sido executado com sucesso, a base de dados pode ser acedida a partir de todos os serviços Azure e do endereço IP configurado.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Descrição |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Comandos do servidor |
| [firewall do servidor az sql](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Comandos de firewall do servidor. |
| [az sql db](/cli/azure/sql/db#az-sql-db-create) | Comandos de base de dados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../az-cli-script-samples-content-guide.md).
