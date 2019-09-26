---
title: Trabalhar com datas no Azure Cosmos DB
description: Saiba mais sobre como trabalhar com datas no Azure Cosmos DB.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: ea7880d051303afad01ad8ba4a2d68d7331c6a89
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291153"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Trabalhar com datas no Azure Cosmos DB
O Azure Cosmos DB oferece flexibilidade de esquema e indexação avançada através de um nativo [JSON](https://www.json.org) modelo de dados. Todos os recursos do Azure Cosmos DB incluindo bases de dados, contentores, documentos e procedimentos armazenados são modelados e armazenados como documentos JSON. Como um requisito para ser portátil, o JSON (e o Azure Cosmos DB) dá suporte apenas a um pequeno conjunto de tipos básicos: String, Number, Boolean, array, Object e NULL. No entanto, o JSON é flexível e permitir que desenvolvedores e estruturas representar os tipos mais complexos, usar esses primitivos e compor-los como objetos ou matrizes. 

Além dos tipos básicos, muitos aplicativos precisam do tipo DateTime para representar datas e carimbos de data/hora. Este artigo descreve como os desenvolvedores podem armazenar, obter e consultar as datas no Azure Cosmos DB com o SDK .NET.

## <a name="storing-datetimes"></a>Armazenar DateTimes

Azure Cosmos DB está no escopo dos tipos JSON que não incluem um tipo DateTime. Portanto, em Azure Cosmos DB, as datas devem ser armazenadas como cadeias de caracteres. Atualmente, Azure Cosmos DB não dá suporte à localização de datas. O formato recomendado para cadeias de caracteres DateTime `YYYY-MM-DDThh:mm:ss.sssZ` em Azure Cosmos DB é o que segue o padrão ISO 8601 UTC. A formatação das cadeias de caracteres nesse formato permitirá a classificação de datas modo lexicográfico. A lógica para lidar com datas não-UTC deve ser definida pelo cliente. A maioria dos aplicativos pode usar a representação de cadeia de caracteres padrão para DateTime pelos seguintes motivos:

* Cadeias de caracteres possam ser comparadas e a ordenação relativa dos valores DateTime é preservada quando eles são transformados em cadeias de caracteres. 
* Essa abordagem não requer qualquer código personalizado ou atributos para a conversão de JSON.
* As datas, conforme armazenada no JSON são humanas legível.
* Essa abordagem pode tirar partido do índice do Azure Cosmos DB para o desempenho de consulta rápida.

Por exemplo, o fragmento seguinte armazena uma `Order` objeto que contém duas propriedades de DateTime - `ShipDate` e `OrderDate` como um documento com o SDK .NET:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Este documento é armazenado no Azure Cosmos DB, da seguinte forma:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Em alternativa, pode armazenar DateTimes como carimbos de data / Unix, ou seja, como um número que representa o número de segundos decorridos desde 1 de Janeiro de 1970. Timestamp de interno do Azure Cosmos DB (`_ts`) propriedade segue essa abordagem. Pode utilizar o [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) classe serializar DateTimes como números. 

## <a name="indexing-datetimes-for-range-queries"></a>Indexação DateTimes para consultas de intervalo
Consultas de intervalo são comuns com valores de DateTime. Por exemplo, se precisar localizar todas as encomendas criadas desde ontem ou localizar todas as encomendas entregues nos últimos cinco minutos, terá de executar consultas de intervalo. Para executar essas consultas com eficiência, tem de configurar sua coleção para indexação de intervalo em cadeias de caracteres.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Pode saber mais sobre como configurar políticas de indexação ao [políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>Consultar DateTimes no LINQ
O SDK de .NET de SQL automaticamente suporta a consulta de dados armazenados no Azure Cosmos DB através de LINQ. Por exemplo, o fragmento seguinte mostra uma consulta LINQ que ordens de filtros que foram enviados nos últimos três dias.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Pode saber mais sobre a linguagem de consulta SQL do Azure Cosmos DB e o fornecedor de LINQ [consultar o Cosmos DB](how-to-sql-query.md).

Neste artigo, vimos como armazenar, indexar e consultar DateTimes no Azure Cosmos DB.

## <a name="next-steps"></a>Próximos Passos
* Transfira e execute o [amostras de código no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Saiba mais sobre [consultas SQL](how-to-sql-query.md)
* Saiba mais sobre [políticas de indexação do Azure Cosmos DB](index-policy.md)
