---
title: ClI exemplo criar uma base de dados Azure SQL
description: Utilize este script de exemplo da CLI do Azure para criar uma base de dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ecfa61f0680e20dd37a1fedc988664afa6e2a83b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067465"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Utilizar a CLI para criar uma base de dados SQL do Azure única e configurar uma regra de firewall

Este exemplo de script da CLI do Azure cria uma base de dados SQL do Azure e configura uma regra de firewall ao nível do servidor. Uma vez que o script tenha sido executado com sucesso, a Base de Dados SQL pode ser acedida a partir de todos os serviços Azure e do endereço IP configurado.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

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

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../sql-database-cli-samples.md).
