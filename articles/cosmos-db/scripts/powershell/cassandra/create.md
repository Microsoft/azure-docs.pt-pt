---
title: Roteiro PowerShell para criar Azure Cosmos DB Cassandra API keyspace e tabela
description: Azure PowerShell script - Azure Cosmos DB criar espaço-chave e tabela Cassandra API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 87c95177970b488fe934355623c095bb93334a3c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488338"
---
# <a name="create-a-keyspace-and-table-for-azure-cosmos-db---cassandra-api"></a>Crie um espaço-chave e uma tabela para Azure Cosmos DB - Cassandra API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-create.ps1 "Create a keyspace and table for Cassandra API")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|**BD do Cosmos para o Azure**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Cria uma conta De Cosmos DB. |
| [Novo AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/new-azcosmosdbcassandrakeyspace) | Cria um Cosmos DB Cassandra API Keyspace. |
| [New-AzCosmosDBCassandraClusterKey](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Cria uma chave de cluster API da Cosmos DB Cassandra. |
| [New-AzCosmosDBCassandraColumn](/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Cria uma Coluna API de Cosmos DB Cassandra. |
| [Novo-AzCosmosDBCassandraSchema](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Cria um Cosmos DB Cassandra API Schema. |
| [New-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/new-azcosmosdbcassandratable) | Cria uma tabela API de Cosmos DB Cassandra. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).