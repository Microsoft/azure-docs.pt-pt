---
title: Consulta de dados geoespaciais com Azure Cosmos DB
description: Consulta de dados espaciais com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77566325"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Consulta de dados geoespaciais com Azure Cosmos DB

Este artigo cobrirá como consultar dados geoespaciais em Azure Cosmos DB usando SQL e LINQ. Atualmente, armazenar e aceder a dados geoespaciais é suportado apenas pelas contas API Da Azure Cosmos DB SQL. A Azure Cosmos DB suporta as seguintes funções integradas do Consórcio Geoespacial Aberto (OGC) para consulta geoespacial. Para obter mais informações sobre o conjunto completo de funções incorporadas na língua SQL, consulte funções do [Sistema de Consulta em Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Funções espaciais SQL incorporadas

Aqui está uma lista de funções do sistema geoespacial úteis para consulta em Azure Cosmos DB:

|**Utilização**|**Descrição**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Devolve a distância entre as duas expressões GeoJSON Point, Polygon ou LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Devolve uma expressão booleana indicando se o primeiro objeto GeoJSON (Point, Polygon ou LineString) está dentro do segundo objeto GeoJSON (Point, Polygon ou LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Devolve uma expressão booleana indicando se os dois objetos GeoJSON especificados (Ponto, Polígono ou LineString) se cruzam.|
|ST_ISVALID| Devolve um valor booleano indicando se a expressão especificada de GeoJSON Point, Polygon ou LineString é válida.|
| ST_ISVALIDDETAILED| Devolve um valor JSON contendo um valor booleano se a expressão especificada de GeoJSON Point, Polygon ou LineString for válida. Se inválido, devolve a razão como valor de cadeia.|

As funções espaciais podem ser usadas para realizar consultas de proximidade contra dados espaciais. Por exemplo, aqui está uma consulta que devolve todos os documentos familiares `ST_DISTANCE` que estão a menos de 30 km do local especificado usando a função incorporada.

**Consulta**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Se incluir a indexação espacial na sua política de indexação, então as "consultas à distância" serão servidas de forma eficiente através do índice. Para obter mais informações sobre a indexação espacial, consulte [a indexação geoespacial](sql-query-geospatial-index.md). Se não tiver um índice espacial para os caminhos especificados, a consulta fará uma digitalização do recipiente.

`ST_WITHIN`pode ser usado para verificar se um ponto está dentro de um Polígono. Geralmente, os polígonos são usados para representar limites como códigos postais, fronteiras do Estado ou formações naturais. Mais uma vez, se incluir a indexação espacial na sua política de indexação, então as consultas "dentro" serão servidas de forma eficiente através do índice.

Os argumentos de `ST_WITHIN` polígono podem conter apenas um único anel, ou seja, os Polígonos não devem conter buracos neles.

**Consulta**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Semelhante ao quão desajustados os tipos funcionam na consulta do Azure Cosmos DB, se o valor de localização especificado em qualquer dos argumentos for mal formado ou inválido, então avalia **indefinidamente** e o documento avaliado a ser ignorado dos resultados da consulta. Se a sua consulta não `ST_ISVALIDDETAILED` devolver resultados, corra para desinbugiar por que o tipo espacial é inválido.
>
>

O Azure Cosmos DB também suporta a realização de consultas inversas, ou seja, pode indexar polígonos ou linhas em Azure Cosmos DB, em seguida, consulta para as áreas que contêm um ponto especificado. Este padrão é comumente usado na logística para identificar, por exemplo, quando um caminhão entra ou sai de uma área designada.

**Consulta**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
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

`ST_ISVALID`e `ST_ISVALIDDETAILED` pode ser usado para verificar se um objeto espacial é válido. Por exemplo, a seguinte consulta verifica a validade de um ponto com um valor de latitude fora de alcance (-132.8). `ST_ISVALID`devolve apenas um valor `ST_ISVALIDDETAILED` booleano, e devolve o Boolean e uma corda contendo a razão pela qual é considerado inválido.

**Consulta**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Resultados**

```json
    [{
      "$1": false
    }]
```

Estas funções também podem ser usadas para validar os Polígonos. Por exemplo, aqui `ST_ISVALIDDETAILED` usamos para validar um Polígono que não está fechado.

**Consulta**

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

## <a name="linq-querying-in-the-net-sdk"></a>Consulta de LINQ no .NET SDK

O SQL .NET SDK também `Distance()` fornecedores de métodos de tub e `Within()` para utilização dentro das expressões LINQ. O fornecedor SQL LINQ traduz este método e chama as chamadas de função incorporadas Equivalentes SQL (ST_DISTANCE e ST_WITHIN respectivamente).

Aqui está um exemplo de uma consulta LINQ que encontra todos `location` os documentos no contentor Azure Cosmos cujo valor está num raio de 30 km do ponto especificado usando LINQ.

**Consulta LINQ para distância**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Da mesma forma, aqui está uma consulta `location` para encontrar todos os documentos que estão dentro da caixa/Polígono especificado.

**Consulta LINQ para Dentro**

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

Agora que aprendeu a começar com apoio geoespacial em Azure Cosmos DB, a seguir pode:

* Saiba mais sobre [a consulta de DB Azure Cosmos](sql-query-getting-started.md)
* Saiba mais sobre os dados de [localização Geospatial e GeoJSON em Azure Cosmos DB](sql-query-geospatial-intro.md)
* Saiba mais sobre [dados espaciais indexados com Azure Cosmos DB](sql-query-geospatial-index.md)
