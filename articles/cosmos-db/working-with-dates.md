---
title: Trabalhar com datas no Azure Cosmos DB
description: Saiba como armazenar, indexar e consultar objetos DataTime em Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273194"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Trabalhar com datas no Azure Cosmos DB

A Azure Cosmos DB oferece flexibilidade de esquemas e indexação rica através de um modelo de dados [nativo da JSON.](https://www.json.org) Todos os recursos do Azure Cosmos DB incluindo bases de dados, contentores, documentos e procedimentos armazenados são modelados e armazenados como documentos JSON. Como um requisito para estar portátil, JSON (e o Azure Cosmos DB) suporta apenas um pequeno conjunto de tipos básicos: cadeia de caracteres, número, booleano, matriz, objeto e Null. No entanto, o JSON é flexível e permitir que desenvolvedores e estruturas representar os tipos mais complexos, usar esses primitivos e compor-los como objetos ou matrizes.

Além dos tipos básicos, muitas aplicações precisam do tipo DateTime para representar datas e carimbos de tempo. Este artigo descreve como os desenvolvedores podem armazenar, obter e consultar as datas no Azure Cosmos DB com o SDK .NET.

## <a name="storing-datetimes"></a>Armazenar DateTimes

Azure Cosmos DB suporta tipos JSON tais como - string, number, boolean, null, array, object. Não suporta diretamente um tipo dateTime. Atualmente, a Azure Cosmos DB não apoia a localização de datas. Então, tens de guardar o DateTimes como cordas. O formato recomendado para cordas DateTime em Azure Cosmos DB é `YYYY-MM-DDThh:mm:ss.sssZ` que segue a norma UTC ISO 8601. Recomenda-se armazenar todas as datas em Azure Cosmos DB como UTC. A conversão das cordas da data para este formato permitirá a triagem lexicograficamente das datas. Se as datas não UTC forem armazenadas, a lógica deve ser tratada ao lado do cliente. Para converter um DataTime local para UTC, a compensação deve ser conhecida/armazenada como uma propriedade no JSON e o cliente pode usar o offset para calcular o valor utc dateTime.

A maioria dos aplicativos pode usar a representação de cadeia de caracteres padrão para DateTime pelos seguintes motivos:

* Cadeias de caracteres possam ser comparadas e a ordenação relativa dos valores DateTime é preservada quando eles são transformados em cadeias de caracteres.
* Essa abordagem não requer qualquer código personalizado ou atributos para a conversão de JSON.
* As datas, conforme armazenada no JSON são humanas legível.
* Essa abordagem pode tirar partido do índice do Azure Cosmos DB para o desempenho de consulta rápida.

Por exemplo, o seguinte snippet armazena um objeto `Order` contendo duas propriedades DateTime - `ShipDate` e `OrderDate` como um documento usando o .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });
```

Este documento é armazenado no Azure Cosmos DB, da seguinte forma:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Em alternativa, pode armazenar DateTimes como carimbos de data / Unix, ou seja, como um número que representa o número de segundos decorridos desde 1 de Janeiro de 1970. A propriedade interna da Azure Cosmos DB (`_ts`) segue esta abordagem. Pode utilizar a classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) para serializar o DateTimes como números.

## <a name="querying-datetimes-in-linq"></a>Consultar DateTimes no LINQ

O SDK de .NET de SQL automaticamente suporta a consulta de dados armazenados no Azure Cosmos DB através de LINQ. Por exemplo, o seguinte corte mostra uma consulta LINQ que filtra as encomendas que foram enviadas nos últimos três dias:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Traduzido para a seguinte declaração da SQL e executado em Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Você pode aprender mais sobre a linguagem de consulta SQL da Azure Cosmos DB e o provedor LINQ na [Querying Cosmos DB em LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexação DateTimes para consultas de intervalo

As consultas são comuns com os valores dateTime. Para executar estas consultas de forma eficiente, deve ter um índice definido sobre quaisquer propriedades no filtro da consulta.

Você pode aprender mais sobre como configurar políticas de indexação em Políticas de [Indexação de Db Da Azure Cosmos](index-policy.md). 

## <a name="next-steps"></a>Passos Seguintes

* Descarregue e execute as amostras de [Código no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Saiba mais sobre [consultas SQL](sql-query-getting-started.md)
* Saiba mais sobre as políticas de indexação do [Azure Cosmos DB](index-policy.md)
