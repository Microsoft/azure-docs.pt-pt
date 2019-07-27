---
title: Exemplo da CLI – Monitorizar e dimensionar uma base de dados SQL do Azure | Microsoft Docs
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
ms.openlocfilehash: 9468f5d631dd713c9e131c63de824c5e552178e3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569920"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Utilizar a CLI para monitorizar e dimensionar uma base de dados SQL

Este exemplo de script da CLI do Azure dimensiona uma base de dados SQL do Azure para um tamanho da computação diferente depois de consultar as informações de tamanho da base de dados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Utilize [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) para obter uma lista de operações executadas na base de dados e [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) para cancelar uma operação de atualização na base de dados.

## <a name="clean-up-deployment"></a>Limpar a implementação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Cria um servidor de banco de dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql#az-sql-show-usage) | Mostra as informações de uso de tamanho de um banco de dados único ou em pool. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Atualiza as propriedades da base de dados (como o escalão de serviço ou o tamanho da computação) ou move uma base de dados para, de ou entre conjuntos elásticos. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../sql-database-cli-samples.md).
