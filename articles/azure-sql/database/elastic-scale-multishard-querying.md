---
title: Bases de dados de consultas
description: Executar consultas através de fragmentos usando a biblioteca de clientes de base de dados elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5a0dd12efb9d94bda264b3bd04b05cdc3df917e5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786637"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Consulta multi-fragmentos usando ferramentas de base de dados elásticas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>Descrição geral

Com as [ferramentas Elastic Database,](elastic-scale-introduction.md)pode criar soluções de base de dados com fragmentos. **A consulta multi-fragmentos** é usada para tarefas como a recolha/reporte de dados que requerem a execução de uma consulta que se estende por vários fragmentos. (Contraste isto com o [encaminhamento dependente de dados,](elastic-scale-data-dependent-routing.md)que executa todos os trabalhos num único fragmento.)

1. Obtenha um **RangeShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET)](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)ou **ListShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) utilizando o método **TryGetRangeShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET),](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)o **método TryGetListShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET),](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)ou o método **GetShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap) Consulte [a Construção de um ShardMapManager](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [Obtenha um RangeShardMap ou ListShardMap](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Criar um objeto **MultiShardConnection** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)
3. Criar um **MultiShardStatement ou MultiShardCommand** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Desaprote a **propriedade CommandText** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET)](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)para um comando T-SQL.
5. Execute o comando chamando o método **ExecuteQueryAsync ou ExecutReader** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)
6. Ver os resultados utilizando a classe **MultiShardResultSet ou MultiShardDataReader** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)

## <a name="example"></a>Exemplo

O código que se segue ilustra a utilização de consultas multi-fragmentos utilizando um dado **ShardMap** denominado *myShardMap* .

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

Uma diferença fundamental é a construção de ligações multi-fragmentos. Onde **a SqlConnection** opera numa base de dados individual, o **MultiShardConnection** leva como entrada uma **_coleção de fragmentos._*Povoar a coleção de fragmentos de um mapa de fragmentos. A consulta é então executada na recolha de fragmentos utilizando _* SEMântica UNIÃO TODOS** para montar um único resultado global. Opcionalmente, o nome do fragmento de onde a linha é originária pode ser adicionado à saída utilizando a propriedade **ExecutionOptions** no comando.

Note a chamada para **myShardMap.GetShards()** . Este método recupera todos os fragmentos do mapa de fragmentos e fornece uma maneira fácil de executar uma consulta em todas as bases de dados relevantes. A recolha de fragmentos para uma consulta multi-fragmentos pode ser aperfeiçoada ainda mais através da realização de uma consulta LINQ sobre a coleção devolvida da chamada para **myShardMap.GetShards()** . Em combinação com a política de resultados parciais, a capacidade atual em consultas multi-fragmentos foi projetada para funcionar bem para dezenas até centenas de fragmentos.

Uma limitação com consulta multi-fragmentos é atualmente a falta de validação para fragmentos e fragmentos que são consultados. Embora o encaminhamento dependente de dados verifique que um dado fragmento faz parte do mapa de fragmentos no momento da consulta, as consultas multi-fragmentos não efetuam esta verificação. Isto pode levar a consultas multi-fragmentos em bases de dados que foram removidas do mapa de fragmentos.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Consultas multi-fragmentos e operações de fusão dividida

As consultas multi-fragmentos não verificam se os fragmentos na base de dados consultadas estão a participar em operações de fusão em curso. (Ver [Escala usando a ferramenta de fusão split da Base de Dados Elástica](elastic-scale-overview-split-and-merge.md).) Isto pode levar a inconsistências onde as filas do mesmo shardlet mostram para várias bases de dados na mesma consulta multi-fragmentos. Esteja ciente destas limitações e considere drenar as operações de fusão em curso e alterações no mapa de fragmentos enquanto realiza consultas multi-fragmentos.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]