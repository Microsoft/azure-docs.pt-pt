---
title: Começar com consultas SQL em Azure Cosmos DB
description: Aprenda a usar consultas SQL para consultar dados do Azure Cosmos DB. Pode enviar dados de amostra para um contentor em Azure Cosmos DB e questioná-lo.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873340"
---
# <a name="getting-started-with-sql-queries"></a>Começar com consultas SQL

As contas da Azure Cosmos DB SQL API suportam itens de consulta utilizando linguagem de consulta estruturada (SQL) como uma linguagem de consulta JSON. Os objetivos de design da linguagem de consulta Azure Cosmos DB são para:

* Apoie o SQL, uma das línguas de consulta mais familiares e populares, em vez de inventar uma nova linguagem de consulta. A SQL fornece um modelo formal de programação para consultas ricas sobre itens JSON.  

* Use o modelo de programação do JavaScript como base para a linguagem de consulta. O sistema de tipo javaScript, avaliação de expressão e invocação de funções são as raízes da API SQL. Estas raízes fornecem um modelo de programação natural para funcionalidades como projeções relacionais, navegação hierárquica em itens JSON, auto-uniões, consultas espaciais e invocação de funções definidas pelo utilizador (UDFs) escritas inteiramente no JavaScript.

## <a name="upload-sample-data"></a>Enviar dados da amostra

Na sua conta SQL API Cosmos `Families`DB, crie um recipiente chamado . Crie dois itens Simples JSON no recipiente. Pode executar a maior parte das consultas de amostra nos médicos de consulta do Azure Cosmos DB utilizando este conjunto de dados.

### <a name="create-json-items"></a>Criar itens JSON

O seguinte código cria dois itens Simples JSON sobre famílias. Os itens simples da JSON para as famílias Andersen e Wakefield incluem pais, crianças e seus animais de estimação, endereço e informações de registo. O primeiro item tem cordas, números, booleans, matrizes e propriedades aninhadas.


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

O segundo `givenName` item `familyName` usa `firstName` `lastName`e em vez de e .

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

Experimente algumas consultas contra os dados da JSON para entender alguns dos aspectos-chave da linguagem de consulta SQL da Azure Cosmos DB.

A seguinte consulta devolve os itens `id` onde `AndersenFamily`o campo corresponde . Como é uma `SELECT *` consulta, a saída da consulta é o item completo da JSON. Para mais informações sobre a sintaxe SELECT, consulte a [declaração SELECT](sql-query-select.md). 

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

A consulta seguinte reforma a saída jSON em uma forma diferente. A consulta projeta um novo `Family` objeto JSON `Name` `City`com dois campos selecionados, e, quando a cidade de endereço é o mesmo que o estado. "NY, NY" corresponde a este caso.

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

A seguinte consulta devolve todos os nomes `id` dadas das crianças da família cujos fósforos `WakefieldFamily`, ordenados pela cidade.

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

Os exemplos anteriores mostram vários aspetos da linguagem de consulta cosmos DB:  

* Uma vez que a SQL API trabalha nos valores jSON, lida com entidades em forma de árvore em vez de linhas e colunas. Pode referir-se aos nós das árvores em `Node1.Node2.Node3…..Nodem`qualquer profundidade arbitrária, como, semelhante à referência em duas partes no `<table>.<column>` ANSI SQL.

* Como a linguagem de consulta funciona com dados sem esquema, o sistema de tipo deve ser ligado dinamicamente. A mesma expressão poderia produzir diferentes tipos em diferentes itens. O resultado de uma consulta é um valor JSON válido, mas não é garantido ser de um esquema fixo.  

* A Azure Cosmos DB suporta apenas itens Estritamente JSON. O sistema de tipo e expressões são restritos a lidar apenas com tipos JSON. Para mais informações, consulte a [especificação JSON](https://www.json.org/).  

* Um recipiente Cosmos é uma coleção sem esquemas de itens JSON. As relações dentro e fora dos itens de contentores são implicitamente capturadas pela contenção, não pelas principais relações fundamentais e estrangeiras. Esta funcionalidade é importante para as juntas intra-item discutidas mais tarde neste artigo.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)
