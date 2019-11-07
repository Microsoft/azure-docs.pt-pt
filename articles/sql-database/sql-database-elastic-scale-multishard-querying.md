---
title: Consultar bancos de dados SQL do Azure fragmentados
description: Executar consultas entre fragmentos usando a biblioteca de cliente do banco de dados elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 108da61323f61b009fbfdedac4cd345c6b87a7be
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690172"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Consulta de vários fragmentos usando ferramentas de banco de dados elástico

## <a name="overview"></a>Descrição geral

Com as [ferramentas de banco de dados elástico](sql-database-elastic-scale-introduction.md), você pode criar soluções de banco de dados fragmentadas. **A consulta de vários fragmentos** é usada para tarefas como coleta/relatório de dados que exigem a execução de uma consulta que se estende por vários fragmentos. (Compare com o [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md), que executa todo o trabalho em um único fragmento.)

1. Obtenha um **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) ou **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) usando o **método trygetrangeshardmap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), o **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [ .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) ou o método **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)). Consulte [construindo um ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [obter um RangeShardMap ou ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Crie um objeto **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Crie um **MultiShardStatement ou MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Defina a **propriedade CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) para um comando T-SQL.
5. Execute o comando chamando o método **ExecuteQueryAsync ou ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
6. Exiba os resultados usando a classe **MultiShardResultSet ou MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)).

## <a name="example"></a>Exemplo

O código a seguir ilustra o uso de consultas de vários fragmentos usando um determinado **ShardMap** chamado *myShardMap*.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Uma diferença importante é a construção de conexões de vários fragmentos. Quando **SqlConnection** opera em um banco de dados individual, o **MultiShardConnection** usa uma ***coleção de fragmentos*** como sua entrada. Popular a coleção de fragmentos de um mapa de fragmentos. Em seguida, a consulta é executada na coleção de fragmentos usando a semântica **Union All** para montar um único resultado geral. Opcionalmente, o nome do fragmento do qual a linha se origina pode ser adicionado à saída usando a propriedade **executionoptions** no comando.

Observe a chamada para **myShardMap.** getfragments (). Esse método recupera todos os fragmentos do mapa de fragmentos e fornece uma maneira fácil de executar uma consulta em todos os bancos de dados relevantes. A coleção de fragmentos para uma consulta de vários fragmentos pode ser refinada com a execução de uma consulta LINQ sobre a coleção retornada da chamada para MyShardMap. getfragments **()** . Em combinação com a política de resultados parciais, a funcionalidade atual da consulta de vários fragmentos foi projetada para funcionar bem para dezenas de centenas de fragmentos.

Uma limitação com consulta de vários fragmentos atualmente é a falta de validação para fragmentos e shardlets que são consultados. Embora o roteamento dependente de dados verifique se um determinado fragmento faz parte do mapa de fragmentos no momento da consulta, as consultas de vários fragmentos não executam essa verificação. Isso pode levar a consultas de vários fragmentos em execução em bancos de dados que foram removidos do mapa de fragmentos.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Consultas de vários fragmentos e operações de divisão/mesclagem

As consultas de vários fragmentos não verificam se shardlets no banco de dados consultado estão participando de operações de divisão/mesclagem em andamento. (Consulte [dimensionamento usando a ferramenta de divisão/mesclagem do banco de dados elástico](sql-database-elastic-scale-overview-split-and-merge.md).) Isso pode levar a inconsistências em que as linhas do mesmo shardlet mostram para vários bancos de dados na mesma consulta de vários fragmentos. Esteja atento a essas limitações e considere a descarregamento de operações de divisão/mesclagem contínuas e alterações no mapa de fragmentos durante a execução de consultas de vários fragmentos.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]