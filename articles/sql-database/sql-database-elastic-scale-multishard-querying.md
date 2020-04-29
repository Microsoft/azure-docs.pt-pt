---
title: Bases de dados de consultas
description: Execute consultas em fragmentos usando a biblioteca de clientes de base de dados elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ae14a9fd8fc8479eac596fb694e12e3e0a9027f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067301"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Consulta multi-fragmento utilizando ferramentas de base de dados elásticas

## <a name="overview"></a>Descrição geral

Com as [ferramentas Elastic Database,](sql-database-elastic-scale-introduction.md)pode criar soluções de base de dados esfartos. **A consulta multi-fragmento** é usada para tarefas como a recolha/reportagem de dados que requerem executar uma consulta que se estende por vários fragmentos. (Contraste isto com o [encaminhamento dependente de dados,](sql-database-elastic-scale-data-dependent-routing.md)que executa todos os trabalhos num único fragmento.)

1. Obtenha um **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)ou **ListShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) utilizando o **método TryGetRangeShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET),](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)o **TryGetListShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)ou o método **GetShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap) Consulte [a construção de um ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [obtenha um RangeShardMap ou ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Crie um objeto **MultiShardConnection** [(Java,](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection) [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)
3. Crie um **MultiShardStatement ou MultiShardCommand** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)
4. Desloque a **propriedade CommandText** [(Java,](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement) [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) para um comando T-SQL.
5. Execute o comando chamando o método **ExecuteQueryAsync ou ExecuteReader** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)
6. Ver os resultados utilizando a classe **MultiShardResultSet ou MultiShardDataReader** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)

## <a name="example"></a>Exemplo

O código que se segue ilustra o uso de consultas multi-fragmentos utilizando um dado **ShardMap** chamado *myShardMap*.

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

Uma diferença fundamental é a construção de ligações multi-fragmentos. Quando a **SqlConnection** opera numa base de dados individual, a **MultiShardConnection** toma como entrada uma ***coleção de fragmentos.*** Povoe a coleção de fragmentos de um mapa de fragmentos. A consulta é então executada na coleção de fragmentos usando semântica **UNION ALL** para reunir um único resultado global. Opcionalmente, o nome do fragmento de onde provém a linha pode ser adicionado à saída utilizando a propriedade **ExecutionOptions** no comando.

Note a chamada para **myShardMap.GetShards()**. Este método recupera todos os fragmentos do mapa do fragmento e fornece uma maneira fácil de executar uma consulta em todas as bases de dados relevantes. A coleção de fragmentos para uma consulta multi-fragmento pode ser refinada ainda mais através da realização de uma consulta LINQ sobre a coleção devolvida da chamada para **myShardMap.GetShards()**. Em combinação com a política de resultados parciais, a capacidade atual na consulta de vários fragmentos foi projetada para funcionar bem para dezenas de fragmentos.

Uma limitação com consulta multi-fragmento é atualmente a falta de validação para fragmentos e fragmentos que são consultados. Enquanto o encaminhamento dependente de dados verifica que um determinado fragmento faz parte do mapa do fragmento no momento da consulta, as consultas multi-fragmentos não realizam esta verificação. Isto pode levar a consultas multi-fragmentos em bases de dados que foram removidas do mapa do fragmento.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Consultas multi-fragmentos e operações de fusão de divisões

As consultas multi-fragmentos não verificam se os fragmentos na base de dados consultada estão a participar em operações de fusão contínua. (Ver [escala utilizando a ferramenta de fusão de base de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md).) Isto pode levar a inconsistências onde as filas do mesmo fragmento mostram várias bases de dados na mesma consulta multi-fragmento. Esteja ciente destas limitações e considere drenar as operações de fusão contínua e alterações no mapa do fragmento enquanto executa consultas multi-fragmentos.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]