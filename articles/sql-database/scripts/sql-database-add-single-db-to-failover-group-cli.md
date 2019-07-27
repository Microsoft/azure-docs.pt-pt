---
title: Exemplo de CLI – adicionar banco de dados individual ao grupo de failover – banco de dados SQL do Azure
description: CLI do Azure script de exemplo para criar um banco de dados individual do banco de dados SQL do Azure, adicioná-lo a um grupo de failover e failover de teste.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b80be0db41ea8bd3996a7b8843c80f510289545f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570045"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Utilize a CLI para mover uma base de dados SQL do Azure num conjunto elástico SQL

Este exemplo de script do PowerShell cria um banco de dados individual, cria um grupo de failover, adiciona o banco de dados a ele e testa o failover. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [conjunto de contas AZ](/cli/azure/account?view=azure-cli-latest#az-account-set) | Define uma assinatura como a assinatura ativa atual. | 
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor de banco de dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de um servidor. | 
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Cria um grupo de failover. | 
| [AZ SQL failover – lista de grupos](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lista os grupos de failover em um servidor. |
| [AZ SQL failover-conjunto de grupos-primário](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Defina o primário do grupo de failover fazendo failover de todos os bancos de dados do servidor primário atual. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../sql-database-cli-samples.md).
