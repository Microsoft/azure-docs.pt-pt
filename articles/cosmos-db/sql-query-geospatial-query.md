---
title: Consulta de dados geoespaciais com Azure Cosmos DB
description: Consulta de dados espaciais com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336134"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Consulta de dados geoespaciais com Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo abrangerá como consultar dados geoespaciais em Azure Cosmos DB usando SQL e LINQ. Atualmente, armazenar e aceder a dados geoespaciais é suportado apenas pelas contas Azure Cosmos DB SQL API. A Azure Cosmos DB suporta as seguintes funções de Open Geospatial Consortium (OGC) para consulta geoespacial. Para obter mais informações sobre o conjunto completo de funções incorporadas no idioma SQL, consulte [as funções do sistema de consulta em Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Funções espaciais SQL incorporadas

Aqui está uma lista de funções do sistema geoespacial úteis para consulta em Azure Cosmos DB:

|**Utilização**|**Descrição**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Devolve a distância entre as duas expressões GeoJSON Point, Polygon ou LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Devolve uma expressão booleana indicando se o primeiro objeto GeoJSON (Point, Polygon ou LineString) está dentro do segundo objeto GeoJSON (Point, Polygon ou LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Retorna uma expressão booleana indicando se os dois objetos GeoJSON especificados (Point, Polygon ou LineString) se cruzam.|
|ST_ISVALID| Devolve um valor Boolean que indica se a expressão GeoJSON, Polygon ou LineString especificada é válida.|
| ST_ISVALIDDETAILED| Devolve um valor JSON contendo um valor Boolean se a expressão GeoJSON, Polygon ou LineString especificada for válida. Se inválido, devolve a razão como um valor de cadeia.|

As funções espaciais podem ser usadas para realizar consultas de proximidade contra dados espaciais. Por exemplo, aqui está uma consulta que devolve todos os documentos familiares que estão a menos de 30 km do local especificado usando a `ST_DISTANCE` função incorporada.

**Query**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Se incluir indexação espacial na sua política de indexação, então as "consultas à distância" serão servidas de forma eficiente através do índice. Para obter mais informações sobre a indexação espacial, consulte [a indexação geoespacial](sql-query-geospatial-index.md). Se não tiver um índice espacial para os caminhos especificados, a consulta fará uma verificação do recipiente.

`ST_WITHIN` pode ser usado para verificar se um ponto está dentro de um Polígono. Normalmente, os polígonos são usados para representar limites como códigos postais, fronteiras do estado ou formações naturais. Mais uma vez, se incluir indexação espacial na sua política de indexação, então as consultas "dentro" serão servidas de forma eficiente através do índice.

Os argumentos do `ST_WITHIN` polígono podem conter apenas um único anel, ou seja, os Polígonos não devem conter buracos neles.

**Query**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Semelhante à forma como os tipos desajustados funcionam na consulta DB do Azure Cosmos, se o valor de localização especificado em ambos os argumentos for mal formado ou inválido, então avalia-se **a indefinido** e o documento avaliado a ser ignorado dos resultados da consulta. Se a sua consulta não devolver resultados, corra `ST_ISVALIDDETAILED` para depurar por que o tipo espacial é inválido.
>
>

A Azure Cosmos DB também suporta a realização de consultas inversas, ou seja, pode indexar polígonos ou linhas em Azure Cosmos DB, em seguida, consultar as áreas que contêm um ponto especificado. Este padrão é comumente usado em logística para identificar, por exemplo, quando um caminhão entra ou sai de uma área designada.

**Query**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Resultados**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` e `ST_ISVALIDDETAILED` pode ser usado para verificar se um objeto espacial é válido. Por exemplo, a seguinte consulta verifica a validade de um ponto com um valor de latitude fora de alcance (-132.8). `ST_ISVALID` devolve apenas um valor Boolean, e `ST_ISVALIDDETAILED` devolve o Boolean e uma cadeia contendo a razão pela qual é considerado inválido.

**Query**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Resultados**

```json
    [{
      "$1": false
    }]
```

Estas funções também podem ser usadas para validar polígonos. Por exemplo, aqui usamos `ST_ISVALIDDETAILED` para validar um Polígono que não está fechado.

**Query**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Resultados**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Consulta linq no .NET SDK

O SQL .NET SDK também fornece métodos de stub `Distance()` e `Within()` para utilização dentro das expressões LINQ. O fornecedor SQL LINQ traduz este método chama às chamadas de função incorporadas sql equivalentes (ST_DISTANCE e ST_WITHIN respectivamente).

Aqui está um exemplo de uma consulta linq que encontra todos os documentos no contentor Azure Cosmos cujo `location` valor está num raio de 30 km do ponto especificado usando LINQ.

**Consulta linq para distância**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Da mesma forma, aqui está uma consulta para encontrar todos os documentos que `location` estão dentro da caixa/polígono especificado.

**Consulta linq para Dentro**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a começar com o suporte geoespacial em Azure Cosmos DB, a seguir pode:

* Saiba mais sobre [a Consulta DB do Azure Cosmos](sql-query-getting-started.md)
* Saiba mais sobre [os dados de localização geoespaciais e geojson em Azure Cosmos DB](sql-query-geospatial-intro.md)
* Saiba mais sobre [os dados espaciais do Índice com a Azure Cosmos DB](sql-query-geospatial-index.md)
