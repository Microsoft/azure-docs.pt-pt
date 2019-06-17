---
title: Consultar bases de dados SQL do Azure em partição horizontal | Documentos da Microsoft
description: Execute consultas em partições horizontais com a biblioteca de cliente de base de dados elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 35759f03d7cf09a4114ca6dca74bd3ee92fdcbfa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761697"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Consultas de vários fragmentos usando ferramentas de bases de dados elásticas

## <a name="overview"></a>Descrição geral

Com o [ferramentas de bases de dados elásticas](sql-database-elastic-scale-introduction.md), pode criar soluções de base de dados em partição horizontal. **Consultas de vários fragmentos** é utilizado para tarefas como coleção/relatórios de dados que requerem a execução de uma consulta que aproveita ao máximo através de vários shards. (Compare isso [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md), que executa todo o trabalho numa única partição horizontal.)

1. Obter um **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) ou **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) com o **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), a **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), ou a **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) método. Ver [construir um ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [receber um RangeShardMap ou ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Criar uma **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) objeto.
3. Criar uma **MultiShardStatement ou MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Definir o **vlastnost CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) para um comando T-SQL.
5. Execute o comando ao chamar o **ExecuteQueryAsync ou ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) método.
6. Ver os resultados usando a **MultiShardResultSet ou MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) classe.

## <a name="example"></a>Exemplo

O código a seguir ilustra a utilização de vários fragmentos como consultar usando um determinado **ShardMap** com o nome *myShardMap*.

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

Uma diferença-chave é a construção de ligações de vários fragmentos. Em que **SqlConnection** opera numa base de dados individual, o **MultiShardConnection** demora um ***coleção de partições horizontais*** como entrada. Popular a coleção de partições horizontais de um mapa de partições horizontais. A consulta, em seguida, é executada na coleção de partições horizontais usando **UNION ALL** semântica para montar um único resultado geral. Opcionalmente, o nome de partição horizontal em que a linha são originados por pode ser adicionado para a saída com o **ExecutionOptions** propriedade no comando.

Observe a chamada para **myShardMap.GetShards()** . Esse método obtém todas as partições horizontais de mapa de partições horizontais e fornece uma forma fácil de executar uma consulta em todas as bases de dados relevantes. A coleção de partições horizontais para uma consulta de vários fragmentos pode ser refinada ainda mais ao executar uma consulta LINQ sobre a recolha retornado da chamada para **myShardMap.GetShards()** . Em combinação com a política de resultados parciais, a capacidade atual de consultas de vários fragmentos foi projetada para funcionar bem para dezenas até centenas de partições horizontais.

Uma limitação com consultas de vários fragmentos atualmente é a falta de validação para as partições horizontais e shardlets que são consultados. Embora o encaminhamento dependente de dados verifica se o determinada partição horizontal é parte do mapa de partições horizontais no momento da consulta, consultas de vários fragmentos não executar esta verificação. Isso pode levar a consultas de vários fragmentos em execução nas bases de dados que foram removidos do mapa de partições horizontais.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Consultas de vários fragmentos e operações de dividir / unir

Consultas de vários fragmentos não verificar se shardlets na base de dados consultado está participando de operações de divisão / intercalação em curso. (Consulte [dimensionar com a ferramenta de dividir / unir da base de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md).) Isso pode levar a inconsistências em que as linhas a partir do mesmo shardlet mostram para várias bases de dados da mesma consulta de vários fragmentos. Lembre-se de que essas limitações e considere drenagem operações de divisão / intercalação em curso e as alterações para o mapa de partições horizontais ao efetuar consultas de vários fragmentos.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]