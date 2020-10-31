---
title: Como consultar dados de tabela no Azure Cosmos DB?
description: Saiba como consultar os dados armazenados na conta Azure Cosmos DB Table API utilizando filtros OData e consultas LINQ
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 06/05/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: e184d85e3daee41f530334aa0034fc98f40a8766
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099229"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Tutorial: Consultar o Azure Cosmos DB através da API de Tabela
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

A [API de Tabela](table-introduction.md) do Azure Cosmos DB suporta as consultas OData e [LINQ](/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) relativamente a dados de chave/valor (tabela).  

Este artigo abrange as seguintes tarefas:

> [!div class="checklist"]
> * Consultar dados com a API de Tabela

As consultas neste artigo utilizam a tabela `People` de exemplo seguinte:

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 |

Veja [Consultar Tabelas e Entidades](/rest/api/storageservices/fileservices/querying-tables-and-entities) para obter detalhes sobre como consultar através da API de Tabela.

Para obter mais informações sobre as capacidades avançadas do Azure Cosmos DB, veja [API de Tabela do Azure Cosmos DB](table-introduction.md) e [Desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Para estas consultas funcionarem, tem de ter uma conta do Azure Cosmos DB e dados de entidade no contentor. Não tem qualquer um destes? Conclua o [início rápido de cinco minutos](create-table-dotnet.md) ou o [tutorial do programador](tutorial-develop-table-dotnet.md) para criar uma conta e povoar a base de dados.

## <a name="query-on-partitionkey-and-rowkey"></a>Consultar em PartitionKey e RowKey

Uma vez que as propriedades PartitionKey e RowKey formam a chave primária de uma entidade, pode utilizar a seguinte sintaxe especial para identificar a entidade:

**Query**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```

**Resultados**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Em alternativa, pode especificar estas propriedades como parte da opção `$filter`, conforme mostrado na secção seguinte. Tenha em atenção que os nomes das propriedades chave e os valores constantes são sensíveis a maiúsculas e minúsculas. As propriedades PartitionKey e RowKey são do tipo Cadeia.

## <a name="query-by-using-an-odata-filter"></a>Consultar com um filtro OData

Quando estiver a criar uma cadeia de filtro, não se esqueça destas regras:

* Utilize os operadores lógicos definidos pela Especificação do Protocolo OData para comparar uma propriedade a um valor. Tenha em atenção que não é possível comparar uma propriedade a um valor dinâmico. Um lado da expressão tem de ser uma constante.
* O nome da propriedade, o operador e o valor constante têm de estar separados por espaços com codificação URL. Um espaço tem codificação URL como `%20`.
* Todas as partes da cadeia de filtro são sensíveis a maiúsculas e minúsculas.
* O valor constante tem de ser do mesmo tipo de dados da propriedade para que o filtro devolva resultados válidos. Para obter mais informações sobre os tipos de propriedade suportados, veja [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](/rest/api/storageservices/understanding-the-table-service-data-model).

Segue-se uma consulta de exemplo que mostra como filtrar pelas propriedades PartitionKey e Email com um `$filter` OData.

**Query**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Para obter mais informações sobre como criar expressões de filtro para vários tipos de dados, veja [Consultar Tabelas e Entidades](/rest/api/storageservices/querying-tables-and-entities).

**Resultados**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Smith |Ben | Ben@contoso.com| 425-555-0102 |

As consultas sobre as propriedades da hora de data não devolvem quaisquer dados quando executados na Tabela API da Azure Cosmos DB. Enquanto as lojas de armazenamento Azure Table datam valores com granularidade temporal de carrapatos, a Tabela API em Azure Cosmos DB usa a  `_ts` propriedade. A `_ts` propriedade está em um segundo nível de granularidade, que não é um filtro OData. Assim, as consultas sobre as propriedades do timetamp são bloqueadas pela Azure Cosmos DB. Como uma solução alternativa, você pode definir uma data personalizada ou uma propriedade de tipo de dados longo e definir o valor da data do cliente.

## <a name="query-by-using-linq"></a>Consultar com o LINQ 
Também pode consultar com o LINQ, que traduz as expressões de consulta OData correspondentes. Segue-se um exemplo de como criar consultas com o SDK .NET:

```csharp
IQueryable<CustomerEntity> linqQuery = table.CreateQuery<CustomerEntity>()
            .Where(x => x.PartitionKey == "4")
            .Select(x => new CustomerEntity() { PartitionKey = x.PartitionKey, RowKey = x.RowKey, Email = x.Email });
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a consultar com a API de Tabela

Agora pode avançar para o tutorial seguinte para saber como distribuir dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir dados globalmente](tutorial-global-distribution-table.md)
