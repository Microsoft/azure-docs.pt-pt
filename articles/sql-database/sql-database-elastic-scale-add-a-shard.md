---
title: Adicionando um fragmento usando ferramentas de banco de dados elástico
description: Como usar APIs de escala elástica para adicionar novos fragmentos a um conjunto de fragmentos.
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
ms.openlocfilehash: 3243c8dfcfa83129f57bcd3ea7969fb4f8b07156
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690346"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Adicionando um fragmento usando ferramentas de banco de dados elástico

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para adicionar um fragmento a um novo intervalo ou chave

Geralmente, os aplicativos precisam adicionar novos fragmentos para manipular dados que são esperados de novas chaves ou intervalos de chaves, para um mapa de fragmentos que já existe. Por exemplo, um aplicativo fragmentado por ID de locatário pode precisar provisionar um novo fragmento para um novo locatário, ou os dados fragmentados mensalmente podem precisar de um novo fragmento provisionado antes do início de cada novo mês.

Se o novo intervalo de valores de chave ainda não fizer parte de um mapeamento existente, será simples adicionar o novo fragmento e associar a nova chave ou o intervalo a esse fragmento.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemplo: adicionando um fragmento e seu intervalo a um mapa de fragmentos existente

Este exemplo usa o métodos trygetshard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.net](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) os métodos createfragment ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) e cria uma instância do ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) classes. No exemplo a seguir, um banco de dados chamado **sample_shard_2** e todos os objetos de esquema necessários dentro dele foram criados para manter o intervalo [300, 400).  

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

Para a versão do .NET, você também pode usar o PowerShell como uma alternativa para criar um novo Gerenciador de mapa de fragmentos. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Para adicionar um fragmento para uma parte vazia de um intervalo existente

Em algumas circunstâncias, talvez você já tenha mapeado um intervalo para um fragmento e preenchido parcialmente com dados, mas agora deseja que os dados futuros sejam direcionados para um fragmento diferente. Por exemplo, você fragmenta o intervalo por dia e já alocou 50 dias a um fragmento, mas no dia 24, você deseja que os dados futuros se pousem em um fragmento diferente. A ferramenta de [divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) de banco de dados elástico pode executar essa operação, mas se a movimentação de dado não for necessária (por exemplo, dados para o intervalo de dias [25, 50), ou seja, o dia 25 inclusive a 50 exclusivo, ainda não existir), você poderá executar isso totalmente usando o APIs de gerenciamento de mapa de fragmentos diretamente.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemplo: dividindo um intervalo e atribuindo a parte vazia a um fragmento recém-adicionado

Um banco de dados chamado "sample_shard_2" e todos os objetos de esquema necessários dentro dele foram criados.  

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

**Importante**: Use essa técnica somente se você tiver certeza de que o intervalo para o mapeamento atualizado está vazio.  Os métodos anteriores não verificam os dados para o intervalo que está sendo movido, portanto, é melhor incluir verificações em seu código.  Se houver linhas no intervalo que está sendo movido, a distribuição de dados real não corresponderá ao mapa de fragmentos atualizado. Use a [ferramenta de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) para executar a operação, em vez desses casos.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
