---
title: Adicionar um fragmento usando ferramentas de base de dados elásticas
description: Como utilizar APIs de escala elástica para adicionar novos fragmentos a um conjunto de fragmentos.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 4043fd374a314735173a1f07f46c8394592b81e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73823717"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Adicionar um fragmento usando ferramentas de base de dados elásticas

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para adicionar um fragmento para uma nova gama ou chave

As aplicações precisam muitas vezes de adicionar novos fragmentos para lidar com dados que são esperados a partir de novas teclas ou intervalos de chaves, para um mapa de fragmentos que já existe. Por exemplo, uma aplicação apresentada pelo Tenant ID pode ter de fornecer um novo fragmento para um novo inquilino, ou os dados mensais podem precisar de um novo fragmento previsto antes do início de cada novo mês.

Se a nova gama de valores-chave já não faz parte de um mapeamento existente, é simples adicionar o novo fragmento e associar a nova chave ou gama a esse fragmento.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemplo: adicionar um fragmento e a sua gama a um mapa de fragmentos existente

Esta amostra utiliza o TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET)](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))o CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET),](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [métodos .NET,](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) e cria uma instância da classe ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation) Na amostra abaixo, foi criada uma base de dados chamada **sample_shard_2** e todos os objetos de esquemanecessários necessários no seu interior para manter o alcance [300.400).  

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

Em algumas circunstâncias, você pode já ter mapeado um intervalo para um fragmento e parcialmente preenchido com dados, mas agora quer que os próximos dados sejam direcionados para um fragmento diferente. Por exemplo, você se esforça à distância do dia e já alocou 50 dias a um fragmento, mas no dia 24, você quer que os dados futuros aterrem em um fragmento diferente. A ferramenta de fusão de base de dados elástica pode executar esta operação, mas se o movimento de dados não for necessário (por exemplo, dados para o intervalo de dias [25, 50), ou seja, dia 25 inclusive a 50 exclusivo, ainda não existe) pode [executá-lo](sql-database-elastic-scale-overview-split-and-merge.md) inteiramente usando diretamente as APIs de Gestão de Mapas de Fragmentos.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemplo: dividir uma gama e atribuir a porção vazia a um fragmento recém-adicionado

Foi criada uma base de dados chamada "sample_shard_2" e todos os objetos de esquemas necessários no seu interior.  

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

**Importante**: Utilize esta técnica apenas se tiver a certeza de que a gama para o mapeamento atualizado está vazia.  Os métodos anteriores não verificam os dados da gama que está a ser movida, pelo que o melhor é incluir verificações no seu código.  Se existirem filas na gama em mudança, a distribuição real de dados não corresponderá ao mapa de fragmentos atualizado. Utilize a [ferramenta de fusão de divisão](sql-database-elastic-scale-overview-split-and-merge.md) para efetuar a operação nestes casos.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
