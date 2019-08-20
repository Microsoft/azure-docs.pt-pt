---
title: Introdução às consultas SQL no Azure Cosmos DB
description: Introdução às consultas SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 5537133b31bb63c9fa6ac3a52b344f7f1d9c4c8a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614353"
---
# <a name="getting-started-with-sql-queries"></a>Introdução às consultas SQL

Azure Cosmos DB contas da API do SQL dão suporte à consulta de itens usando linguagem SQL (SQL) como uma linguagem de consulta JSON. As metas de design da linguagem de consulta Azure Cosmos DB são:

* Suporte ao SQL, uma das linguagens de consulta mais conhecidas e populares, em vez de inventar uma nova linguagem de consulta. O SQL fornece um modelo de programação formal para consultas avançadas sobre itens JSON.  

* Use o modelo de programação do JavaScript como base para a linguagem de consulta. O sistema de tipos, a avaliação de expressão e a invocação de função do JavaScript são as raízes da API do SQL. Essas raízes fornecem um modelo de programação natural para recursos como projeções relacionais, navegação hierárquica em itens JSON, autojunções, consultas espaciais e invocação de UDFs (funções definidas pelo usuário) escritas inteiramente em JavaScript.

## <a name="upload-sample-data"></a>Carregar dados de exemplo

Na sua API do SQL Cosmos DB conta, crie um contêiner `Families`chamado. Crie dois itens JSON simples no contêiner. Você pode executar a maioria das consultas de exemplo no Azure Cosmos DB documentos de consulta usando esse conjunto de dados.

### <a name="create-json-items"></a>Criar itens JSON

O código a seguir cria dois itens JSON simples sobre famílias. Os itens JSON simples para as famílias Andersen e Barros incluem pais, filhos e seus animais de estimação, endereço e informações de registro. O primeiro item tem cadeias de caracteres, números, Boolianos, matrizes e propriedades aninhadas.


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

O segundo item usa `givenName` e `familyName` em vez `firstName` de `lastName`e.

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

### <a name="query-the-json-items"></a>Consultar os itens JSON

Experimente algumas consultas em relação aos dados JSON para entender alguns dos principais aspectos da linguagem de consulta SQL do Azure Cosmos DB.

A consulta a seguir retorna os itens nos `id` quais o `AndersenFamily`campo corresponde. Como é uma `SELECT *` consulta, a saída da consulta é o item JSON completo. Para obter mais informações sobre a sintaxe SELECT, consulte [Select Statement](sql-query-select.md). 

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

A consulta a seguir reformata a saída JSON em uma forma diferente. A consulta projeta um novo objeto `Family` JSON com dois `Name` campos selecionados e `City`, quando a cidade do endereço é igual ao estado. "NY, NY" corresponde a esse caso.

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

A consulta a seguir retorna todos os nomes de filhos na família cujas `id` correspondências `WakefieldFamily`são ordenadas por cidade.

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

Os exemplos anteriores mostram vários aspectos da linguagem de consulta Cosmos DB:  

* Como a API do SQL funciona em valores JSON, ela lida com entidades em formato de árvore em vez de linhas e colunas. Você pode consultar os nós de árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem`, semelhante à referência de duas partes do `<table>.<column>` no SQL ANSI.

* Como a linguagem de consulta funciona com dados sem esquema, o sistema de tipos deve ser vinculado dinamicamente. A mesma expressão poderia resultar em diferentes tipos nos itens diferentes. O resultado de uma consulta é um valor JSON válido, mas não é garantido que seja de um esquema fixo.  

* Azure Cosmos DB suporta apenas os itens JSON rigorosos. O sistema de tipos e as expressões são restritos para lidar apenas com tipos JSON. Para obter mais informações, consulte a [especificação JSON](https://www.json.org/).  

* Um contêiner cosmos é uma coleção sem esquemas de itens JSON. As relações dentro e entre os itens de contêiner são capturadas implicitamente por contenção, não pelas relações de chave primária e chave estrangeira. Esse recurso é importante para as junções intra-item discutidas posteriormente neste artigo.

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Cláusula SELECT](sql-query-select.md)
