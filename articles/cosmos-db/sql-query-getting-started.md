---
title: Começando com consultas SQL em Azure Cosmos DB
description: Saiba como usar consultas SQL para consultar dados da Azure Cosmos DB. Pode enviar dados de amostra para um contentor em Azure Cosmos DB e questioná-lo.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74873340"
---
# <a name="getting-started-with-sql-queries"></a>Começando com consultas SQL

As contas Azure Cosmos DB SQL API suportam itens de consulta utilizando linguagem de consulta estruturada (SQL) como uma linguagem de consulta JSON. Os objetivos de design da linguagem de consulta Azure Cosmos DB são:

* Suporte o SQL, uma das línguas de consulta mais familiares e populares, em vez de inventar uma nova linguagem de consulta. A SQL fornece um modelo formal de programação para consultas ricas sobre itens JSON.  

* Utilize o modelo de programação do JavaScript como base para a linguagem de consulta. O sistema de tipo JavaScript, a avaliação de expressão e a invocação de funções são as raízes da API SQL. Estas raízes fornecem um modelo de programação natural para funcionalidades como projeções relacionais, navegação hierárquica através de itens JSON, uniões auto-juntas, consultas espaciais e invocação de funções definidas pelo utilizador (UDFs) escritas inteiramente em JavaScript.

## <a name="upload-sample-data"></a>Carregar dados de amostras

Na sua conta DB DA API Cosmos, crie um recipiente chamado `Families` . Crie dois itens JSON simples no recipiente. Você pode executar a maioria das consultas de amostra no Azure Cosmos DB consulta docs usando este conjunto de dados.

### <a name="create-json-items"></a>Criar itens JSON

O seguinte código cria dois simples itens JSON sobre famílias. Os itens JSON simples para as famílias Andersen e Wakefield incluem pais, filhos e seus animais de estimação, endereço e informações de registo. O primeiro item tem cordas, números, Booleans, matrizes e propriedades aninhadas.


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

O segundo artigo utiliza `givenName` e em vez de e `familyName` `firstName` `lastName` .

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

* A Azure Cosmos DB suporta apenas itens JSON rigorosos. O sistema de tipo e expressões são restritos a lidar apenas com tipos de JSON. Para mais informações, consulte a [especificação JSON.](https://www.json.org/)  

* Um recipiente Cosmos é uma coleção livre de esquemas de artigos JSON. As relações dentro e fora dos objetos de contentores são implicitamente capturadas pela contenção, não pelas relações fundamentais e externas. Esta funcionalidade é importante para as juntas intra-item discutidas mais tarde neste artigo.

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)
