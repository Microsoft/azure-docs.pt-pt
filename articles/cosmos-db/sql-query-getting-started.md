---
title: Começando com consultas SQL em Azure Cosmos DB
description: Saiba como usar consultas SQL para consultar dados da Azure Cosmos DB. Pode enviar dados de amostra para um contentor em Azure Cosmos DB e questioná-lo.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: d5d5bc0a108cd08283ea29ce3bdc2de49310c5aa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499565"
---
# <a name="getting-started-with-sql-queries"></a>Introdução às consultas SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Nas contas Azure Cosmos DB SQL API, existem duas formas de ler dados:

**Leituras de pontos** - Pode fazer uma procura de chave/valor numa única *chave de iD* e partição de um item. A combinação *de iD* e partição do item é a chave e o item em si é o valor. Para um documento de 1 KB, as leituras de ponto normalmente custam 1 [unidade de pedido](request-units.md) com uma latência inferior a 10 ms. As leituras de ponto devolvem um único item.

Aqui estão alguns exemplos de como fazer **Leituras de pontos** com cada SDK:

- [SDK do .NET](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)
- [SDK Java](/java/api/com.azure.cosmos.cosmoscontainer.readitem#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](/javascript/api/@azure/cosmos/item#read-requestoptions-)
- [Python SDK](/python/api/azure-cosmos/azure.cosmos.containerproxy#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**Consultas SQL** - Pode consultar dados escrevendo consultas usando a Linguagem de Consulta Estruturada (SQL) como uma linguagem de consulta JSON. As consultas custam sempre pelo menos 2,3 unidades de pedido e, em geral, terão uma latência mais alta e mais variável do que as leituras pontuais. As consultas podem devolver muitos itens.

A maioria das cargas de trabalho pesadas de leitura em Azure Cosmos DB usam uma combinação de leituras de pontos e consultas SQL. Se só precisa de ler um único item, as leituras pontuais são mais baratas e rápidas do que as consultas. As leituras de pontos não precisam de usar o motor de consulta para aceder aos dados e podem ler os dados diretamente. É claro que não é possível que todas as cargas de trabalho leiam exclusivamente dados usando leituras de pontos, por isso o suporte do SQL como uma linguagem de consulta e [indexação schema-agnóstica](index-overview.md) fornecem uma forma mais flexível de aceder aos seus dados.

Aqui estão alguns exemplos de como fazer **consultas SQL** com cada SDK:

- [SDK do .NET](./sql-api-dotnet-v3sdk-samples.md#query-examples)
- [SDK Java](./sql-api-java-sdk-samples.md#query-examples)
- [Node.js SDK](./sql-api-nodejs-samples.md#item-examples)
- [Python SDK](./sql-api-python-samples.md#item-examples)

O resto deste doc mostra como começar a escrever consultas SQL em Azure Cosmos DB. As consultas SQL podem ser executadas através do portal SDK ou Azure.

## <a name="upload-sample-data"></a>Carregar dados de amostras

Na sua conta DB DA API Cosmos, abra o [Data Explorer](./data-explorer.md) para criar um recipiente chamado `Families` . Depois de criado, utilize o navegador de estruturas de dados, para o encontrar e abrir. No seu `Families` recipiente, verá a `Items` opção logo abaixo do nome do recipiente. Abra esta opção e verá um botão, na barra de menus no centro do ecrã, para criar um 'Novo Item'. Utilizará esta funcionalidade para criar os itens JSON abaixo.

### <a name="create-json-items"></a>Criar itens JSON

Os seguintes 2 itens JSON são documentos sobre as famílias Andersen e Wakefield. Incluem pais, filhos e seus animais de estimação, morada e informações de registo. 

O primeiro item tem cordas, números, booleans, matrizes e propriedades aninhadas:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

O segundo artigo utiliza `givenName` e `familyName` em vez `firstName` `lastName` de:

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Consulta dos itens JSON

Experimente algumas consultas contra os dados do JSON para entender alguns dos aspectos-chave da linguagem de consulta SQL da Azure Cosmos DB.

A seguinte consulta devolve os itens em que o `id` campo corresponde `AndersenFamily` . Uma vez que é uma `SELECT *` consulta, a saída da consulta é o item JSON completo. Para obter mais informações sobre a sintaxe SELECT, consulte [a declaração SELECT](sql-query-select.md).

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados da consulta são:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

A seguinte consulta reformata a saída JSON em uma forma diferente. A consulta projeta um novo objeto JSON `Family` com dois campos `Name` selecionados, `City` e, quando a cidade de endereço é a mesma que o estado. "NY, NY" corresponde a este caso.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Os resultados da consulta são:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A seguinte consulta devolve todos os nomes de crianças da família cujos `id` jogos `WakefieldFamily` , ordenados pela cidade.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Os resultados são:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Observações

Os exemplos anteriores mostram vários aspetos da linguagem de consulta Cosmos DB:  

* Uma vez que a SQL API trabalha nos valores JSON, trata de entidades em forma de árvore em vez de linhas e colunas. Pode consultar os nós das árvores a qualquer profundidade arbitrária, `Node1.Node2.Node3…..Nodem` como, semelhante à referência em duas partes do `<table>.<column>` ANSI SQL.

* Como a linguagem de consulta funciona com dados sem esquemas, o sistema de tipo deve ser ligado dinamicamente. A mesma expressão pode produzir diferentes tipos em diferentes itens. O resultado de uma consulta é um valor JSON válido, mas não é garantido ser de um esquema fixo.  

* A Azure Cosmos DB suporta apenas itens JSON rigorosos. O sistema de tipos e as expressões são restringidos para processar apenas os tipos JSON. Para mais informações, consulte a [especificação JSON.](https://www.json.org/)  

* Um recipiente Cosmos é uma coleção livre de esquemas de artigos JSON. As relações dentro e fora dos objetos de contentores são implicitamente capturadas pela contenção, não pelas relações fundamentais e externas. Esta funcionalidade é importante para as juntas intra-item que são [descritas em Joins in Azure Cosmos DB](sql-query-join.md).

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)
