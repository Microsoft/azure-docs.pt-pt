---
title: Trabalhar com datas no Azure Cosmos DB
description: Saiba como armazenar, indexar e consultar objetos DataTime em Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 174279e4bd241ee9b336fc1ce7e0af389d2297a3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667009"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Trabalhar com Datas em Azure Cosmos DB

A Azure Cosmos DB oferece flexibilidade de esquemas e indexação rica através de um modelo de dados [nativo da JSON.](https://www.json.org) Todos os recursos da Azure Cosmos DB, incluindo bases de dados, contentores, documentos e procedimentos armazenados são modelados e armazenados como documentos JSON. Como requisito para ser portátil, a JSON (e a Azure Cosmos DB) suporta apenas um pequeno conjunto de tipos básicos: String, Number, Boolean, Array, Object e Null. No entanto, a JSON é flexível e permite que os desenvolvedores e estruturas representem tipos mais complexos usando estes primitivos e compondo-os como objetos ou matrizes.

Além dos tipos básicos, muitas aplicações precisam do tipo DateTime para representar datas e carimbos de tempo. Este artigo descreve como os desenvolvedores podem armazenar, recuperar e consultar datas em Azure Cosmos DB usando o .NET SDK.

## <a name="storing-datetimes"></a>Armazenar datas

Azure Cosmos DB suporta tipos JSON tais como - string, number, boolean, null, array, object. Não suporta diretamente um tipo dateTime. Atualmente, a Azure Cosmos DB não apoia a localização de datas. Então, tens de guardar o DateTimes como cordas. O formato recomendado para cordas DateTime em `YYYY-MM-DDThh:mm:ss.fffffffZ` Azure Cosmos DB é o seguinte à norma UTC ISO 8601. Recomenda-se armazenar todas as datas em Azure Cosmos DB como UTC. A conversão das cordas da data para este formato permitirá a triagem lexicograficamente das datas. Se as datas não UTC forem armazenadas, a lógica deve ser tratada ao lado do cliente. Para converter um DataTime local para UTC, a compensação deve ser conhecida/armazenada como uma propriedade no JSON e o cliente pode usar o offset para calcular o valor UTC DateTime.

Consultas de intervalo com cordas DateTime, uma vez que os filtros só são suportados se as cordas DateTime estiverem todas em UTC e com o mesmo comprimento. No Azure Cosmos DB, a função do sistema [GetCurrentDateTime](sql-query-getcurrentdatetime.md) devolverá a data e hora `YYYY-MM-DDThh:mm:ss.fffffffZ`atuais da UTC no formato: .

A maioria das aplicações pode utilizar a representação de cadeia padrão para datatime pelas seguintes razões:

* As cordas podem ser comparadas, e a ordem relativa dos valores dateTime é preservada quando são transformadas em cordas.
* Esta abordagem não requer qualquer código ou atributos personalizados para a conversão jSON.
* As datas armazenadas na JSON são legíveis em humanos.
* Esta abordagem pode tirar partido do índice da Azure Cosmos DB para um desempenho rápido de consulta.

Por exemplo, o seguinte snippet armazena um `Order` objeto contendo duas propriedades DateTime - `ShipDate` e `OrderDate` como documento utilizando o .NET SDK:

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

Em alternativa, pode armazenar o DateTimes como selos-relógio Unix, isto é, como um número que representa o número de segundos decorridos desde 1 de janeiro de 1970. A propriedade interna`_ts`da Azure Cosmos DB segue esta abordagem. Pode utilizar a classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) para serializar o DateTimes como números.

## <a name="querying-datetimes-in-linq"></a>Consulta dateTimes em LINQ

O SQL .NET SDK suporta automaticamente os dados de consulta armazenados em Azure Cosmos DB via LINQ. Por exemplo, o seguinte corte mostra uma consulta LINQ que filtra as encomendas que foram enviadas nos últimos três dias:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Traduzido para a seguinte declaração da SQL e executado em Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Você pode aprender mais sobre a linguagem de consulta SQL da Azure Cosmos DB e o provedor LINQ na [Querying Cosmos DB em LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Datas de indexaçãoTimes para consultas de intervalo

As consultas são comuns com os valores dateTime. Para executar estas consultas de forma eficiente, deve ter um índice definido sobre quaisquer propriedades no filtro da consulta.

Você pode aprender mais sobre como configurar políticas de indexação em Políticas de [Indexação de Db Da Azure Cosmos](index-policy.md). 

## <a name="next-steps"></a>Passos Seguintes

* Descarregue e execute as amostras de [Código no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Saiba mais sobre [consultas SQL](sql-query-getting-started.md)
* Saiba mais sobre as políticas de indexação do [Azure Cosmos DB](index-policy.md)
