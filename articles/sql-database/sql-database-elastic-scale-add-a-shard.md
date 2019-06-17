---
title: Adicionar uma partição horizontal com as ferramentas de bases de dados elásticas | Documentos da Microsoft
description: Definir como usar APIs de dimensionamento flexível para adicionar novas partições horizontais para uma partição horizontal.
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
ms.date: 01/03/2019
ms.openlocfilehash: dda3c34dccfdaa041cf9f547244d5529482a3138
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585817"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Adicionar uma partição horizontal com as ferramentas de bases de dados elásticas

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para adicionar uma partição horizontal para um novo intervalo ou uma chave

Aplicativos muitas vezes, precisam de adicionar novas partições horizontais para lidar com dados que são esperados de chaves nova ou intervalos de chaves, para um mapa de partições horizontais que já existe. Por exemplo, uma aplicação em partição horizontal por ID de inquilino pode ter de aprovisionar uma nova partição horizontal para um novo inquilino ou mensal de dados em partição horizontal poderá ter uma nova partição horizontal aprovisionada antes do início de cada mês de novo.

Se o novo intervalo de valores de chave já não faz parte de um mapeamento existente, é simples de adicionar a nova partição horizontal e associar a nova chave ou um intervalo para essa partição horizontal.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemplo: adicionar uma partição horizontal e o seu intervalo a um mapa de partições horizontais existente

Este exemplo utiliza o TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) a CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) métodos e cria uma instância do ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) classe. No exemplo abaixo, uma base de dados com o nome **sample_shard_2** e todos os objetos de esquema necessário dentro dele foram criados para manter o intervalo [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

Para a versão do .NET, pode também utilizar o PowerShell como uma alternativa para criar um novo Gestor de mapas de partições horizontais. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Para adicionar uma partição horizontal para uma parte vazia de um intervalo existente

Em algumas circunstâncias, poderá ter já mapeado um intervalo para uma partição horizontal e parcialmente preenchido com dados, mas pretender agora dados futuros para ser direcionado para uma partição horizontal diferente. Por exemplo, partições horizontais por intervalo de dias e ter já alocados 50 dias para uma partição horizontal, mas no dia 24, pretende que os dados futuros para firmar numa partição horizontal diferente. A base de dados elástica [ferramenta de dividir / unir](sql-database-elastic-scale-overview-split-and-merge.md) podem efetuar esta operação, mas se o movimento de dados não é necessário (por exemplo, dados para o intervalo de dias [25, 50), ou seja, dia 25, inclusive para 50 exclusivos, ainda não existir) pode efetuar este procedimento totalmente diretamente a utilizar as APIs de gestão do mapa de partições horizontais.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemplo: a divisão de um intervalo e atribuir a parte vazia para uma partição horizontal adicionada recentemente

Ter sido criada uma base de dados com o nome "sample_shard_2" e todos os objetos de esquema necessário dentro dela.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Importante**:  Use essa técnica somente se estiver se de que o intervalo para o mapeamento atualizado está vazio.  Os métodos anteriores não verificar dados para o intervalo que está a ser movido, então é melhor incluir verificações em seu código.  Se existirem linhas no intervalo que está a ser movido, a distribuição de dados reais não corresponderá ao mapa de partições horizontais atualizado. Utilize o [ferramenta de dividir / unir](sql-database-elastic-scale-overview-split-and-merge.md) para efetuar a operação em vez disso, nesses casos.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
