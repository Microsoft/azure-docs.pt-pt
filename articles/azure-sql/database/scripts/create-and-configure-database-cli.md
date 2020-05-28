---
title: 'Azure CLI: Criar uma única base de dados'
description: Utilize este script de exemplo Azure CLI para criar uma única base de dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ab847f13b6c75eeb31c4d20877d7a314e53f47ac
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054090"
---
# <a name="use-the-azure-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Utilize o Azure CLI para criar uma única base de dados Azure SQL e configurar uma regra de firewall
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script Azure CLI cria uma Base de Dados Azure SQL e configura uma regra de firewall ao nível do servidor. Uma vez que o script tenha sido executado com sucesso, a Base de Dados SQL pode ser acedida a partir de todos os serviços Azure e do endereço IP configurado.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Comandos do servidor |
| [firewall de servidor az sql](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Comandos de firewall do servidor. |
| [az sql db](/cli/azure/sql/db#az-sql-db-create) | Comandos de base de dados. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../az-cli-script-samples-content-guide.md).
