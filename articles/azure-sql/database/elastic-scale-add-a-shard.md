---
title: Adicionar um fragmento usando ferramentas de base de dados elásticas
description: Como utilizar APIs de escala elástica para adicionar novos fragmentos a um conjunto de fragmentos.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: efab0234d428a8283845946289cdd1e8a17ded26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792060"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Adicionar um fragmento usando ferramentas elásticas de base de dados
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para adicionar um fragmento para uma nova gama ou chave

As aplicações muitas vezes precisam adicionar novos fragmentos para lidar com dados que são esperados a partir de novas teclas ou gamas de chaves, para um mapa de fragmentos que já existe. Por exemplo, um pedido de identificação do inquilino pode precisar de providenciar um novo fragmento para um novo inquilino, ou os dados mensais são necessários para precisar de um novo fragmento previsto antes do início de cada novo mês.

Se a nova gama de valores-chave já não faz parte de um mapeamento existente, é simples adicionar o novo fragmento e associar a nova chave ou gama a esse fragmento.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemplo: adicionar um fragmento e sua gama a um mapa de fragmentos existente

Esta amostra utiliza o TryGetShard[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](/previous-versions/azure/dn823929(v=azure.100))), o CreateShard[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) methods, e cria uma instância da classe ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation) Na amostra abaixo, foi criada uma base de dados denominada **sample_shard_2** e todos os objetos de esquema necessários no seu interior para manter a faixa [300.400).  

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

Para a versão .NET, também pode utilizar o PowerShell como alternativa para criar um novo Shard Map Manager. Um exemplo está disponível [aqui.](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Para adicionar um fragmento para uma parte vazia de uma gama existente

Em algumas circunstâncias, pode já ter mapeado uma gama para um fragmento e parcialmente preenchido com dados, mas agora deseja que os dados futuros sejam direcionados para um fragmento diferente. Por exemplo, você fragmento por dia e já alocou 50 dias a um fragmento, mas no dia 24, você quer que os dados futuros aterrem em um fragmento diferente. A ferramenta de [fusão de bases de dados](elastic-scale-overview-split-and-merge.md) elástica pode realizar esta operação, mas se o movimento de dados não for necessário (por exemplo, dados para o intervalo de dias [25, 50), ou seja, o dia 25 inclusive a 50 exclusivo, ainda não existe), pode efetuar esta operação inteiramente utilizando diretamente as APIs de Gestão de Mapas de Fragmentos.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemplo: dividir um intervalo e atribuir a porção vazia a um fragmento recém-adicionado

Foi criada uma base de dados chamada "sample_shard_2" e todos os objetos de esquema necessários no seu interior.  

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

**Importante**: Utilize esta técnica apenas se tiver a certeza de que o intervalo para o mapeamento atualizado está vazio.  Os métodos anteriores não verificam os dados da gama que está a ser movida, pelo que o melhor é incluir verificações no seu código.  Se existirem linhas na gama que está a ser movida, a distribuição de dados real não corresponderá ao mapa de fragmentos atualizado. Utilize a [ferramenta de fusão dividida](elastic-scale-overview-split-and-merge.md) para efetuar a operação nestes casos.  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]