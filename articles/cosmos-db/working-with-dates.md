---
title: Trabalhar com datas em Azure Cosmos DB
description: Saiba como armazenar, indexar e consultar objetos DataTime em Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4d4e63bdd1e795bc1c599d0eae8a595aa0d643
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359528"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Trabalhar com datas em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB oferece flexibilidade de esquemas e indexação rica através de um modelo de dados [JSON](https://www.json.org) nativo. Todos os recursos DB da Azure Cosmos, incluindo bases de dados, contentores, documentos e procedimentos armazenados são modelados e armazenados como documentos JSON. Como requisito para ser portátil, JSON (e Azure Cosmos DB) suporta apenas um pequeno conjunto de tipos básicos: String, Number, Boolean, Array, Object e Null. No entanto, o JSON é flexível e permite que os desenvolvedores e quadros representem tipos mais complexos usando estes primitivos e compondo-os como objetos ou matrizes.

Além dos tipos básicos, muitas aplicações precisam do tipo DateTime para representar datas e timetamps. Este artigo descreve como os desenvolvedores podem armazenar, recuperar e consultar datas em Azure Cosmos DB usando o .NET SDK.

## <a name="storing-datetimes"></a>Armazenando DataTimes

Azure Cosmos DB suporta tipos de JSON tais como - string, number, boolean, nulo, matriz, objeto. Não suporta diretamente um tipo de DataTime. Atualmente, a Azure Cosmos DB não apoia a localização de datas. Por isso, tens de armazenar o DateTimes como cordas. O formato recomendado para as cordas DateTime em Azure Cosmos DB é `yyyy-MM-ddTHH:mm:ss.fffffffZ` o que segue a norma ISO 8601 UTC. Recomenda-se guardar todas as datas em Azure Cosmos DB como UTC. A conversão das cordas de data para este formato permitirá classificar as datas lexicograficamente. Se as datas não-UTC forem armazenadas, a lógica deve ser tratada do lado do cliente. Para converter uma DataTime local para UTC, o offset deve ser conhecido/armazenado como um imóvel no JSON e o cliente pode usar o offset para calcular o valor UTC DateTime.

As consultas de gama com as cordas DateTime como filtros só são suportadas se as cordas DateTime estiverem todas em UTC e no mesmo comprimento. Em Azure Cosmos DB, a função do sistema [GetCurrentDateTime](sql-query-getcurrentdatetime.md) devolverá o valor de cadeia ISO 8601 atual no formato: `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

A maioria das aplicações pode usar a representação de cadeia padrão para o DateTime pelas seguintes razões:

* As cordas podem ser comparadas e a encomenda relativa dos valores datetime é preservada quando são transformadas em cordas.
* Esta abordagem não requer nenhum código ou atributos personalizados para a conversão JSON.
* As datas armazenadas em JSON são legíveis ao homem.
* Esta abordagem pode tirar partido do índice da Azure Cosmos DB para um desempenho de consulta rápida.

Por exemplo, o seguinte corte armazena um `Order` objeto contendo duas propriedades DateTime - `ShipDate` e como um documento `OrderDate` usando o .NET SDK:

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

Este documento é armazenado em Azure Cosmos DB da seguinte forma:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Em alternativa, pode armazenar DateTimes como semps temporais Unix, isto é, como um número que representa o número de segundos decorridos desde 1 de janeiro de 1970. A propriedade interna de Azure Cosmos DB `_ts` segue esta abordagem. Pode utilizar a classe [UnixDateTimeConverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) para serializar o DateTimes como números.

## <a name="querying-datetimes-in-linq"></a>Consulta de Datas em LINQ

O SQL .NET SDK suporta automaticamente dados de consulta armazenados em Azure Cosmos DB via LINQ. Por exemplo, o seguinte corte mostra uma consulta LINQ que filtra encomendas que foram enviadas nos últimos três dias:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Traduzido para a seguinte declaração SQL e executado em Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Você pode saber mais sobre a linguagem de consulta SQL da Azure Cosmos DB e o provedor linq na [Consulta Cosmos DB em LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexação de DataTimes para consultas de gama

As consultas são comuns com os valores do DateTime. Para executar estas consultas de forma eficiente, tem de ter um índice definido em quaisquer propriedades no filtro da consulta.

Pode aprender mais sobre como configurar políticas de indexação nas [políticas de indexação Azure Cosmos DB](index-policy.md). 

## <a name="next-steps"></a>Passos Seguintes

* Faça o download e execute as amostras de [código no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples)
* Saiba mais sobre [consultas SQL](sql-query-getting-started.md)
* Saiba mais sobre [as Políticas de Indexação DB da Azure Cosmos](index-policy.md)