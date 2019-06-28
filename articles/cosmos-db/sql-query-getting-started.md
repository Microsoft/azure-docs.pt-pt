---
title: Introdução às consultas SQL no Azure Cosmos DB
description: Introdução às consultas SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342544"
---
# <a name="getting-started-with-sql-queries"></a>Introdução às consultas SQL

As contas do Azure Cosmos DB SQL API suportam itens de consultas usando a linguagem SQL (Structured Query) como uma linguagem de consulta JSON. Os objetivos de design da linguagem de consulta do Azure Cosmos DB são:

* Suporta SQL, uma das linguagens de consulta mais populares e familiares, em vez de inventar uma nova linguagem de consulta. SQL fornece um modelo de programação formal para consultas sobre itens JSON.  

* Utilize o modelo de programação do JavaScript como a base para a linguagem de consulta. Sistema de tipos, avaliação de expressões e invocação de função do JavaScript são as raízes da API do SQL. Essas raízes fornecem um modelo de programação natural para funcionalidades como projeções relacionais, navegação hierárquica entre itens JSON, associações automáticas, consultas espaciais e invocação de funções definidas pelo utilizador (UDFs) escritos inteiramente no JavaScript.

## <a name="upload-sample-data"></a>Carregar dados de exemplo

Na sua conta do Cosmos DB com API SQL, criar um contentor chamado `Families`. Crie dois itens JSON simples no contentor. Pode executar a maioria das consultas de exemplo em documentos de consulta do Azure Cosmos DB com este conjunto de dados.

### <a name="create-json-items"></a>Criar itens JSON

O código a seguir cria dois itens JSON simples sobre famílias. Os itens JSON simples para as famílias Andersen e Wakefield incluem elementos principais, filhos e seus animais de estimação, endereço e informações de registo. O primeiro item tem as cadeias de caracteres, números, booleanos, matrizes e propriedades aninhadas.


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

O segundo item utiliza `givenName` e `familyName` em vez de `firstName` e `lastName`.

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

Tente algumas consultas com os dados JSON para compreender alguns dos principais aspectos da linguagem de consulta SQL do Azure Cosmos DB.

A seguinte consulta devolve os itens em que o `id` correspondências de campo `AndersenFamily`. Uma vez que é um `SELECT *` consulta, o resultado da consulta é o item JSON completo. Para obter mais informações sobre a sintaxe SELECIONE, consulte [instrução SELECT](sql-query-select.md). 

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

A seguinte consulta reformata a saída JSON para um formato diferente. A consulta projeta um nova JSON `Family` objeto com dois campos selecionados, `Name` e `City`, quando a cidade de endereço é o mesmo que o estado. Neste caso, corresponde a "NY, NY".

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

A seguinte consulta devolve todos os nomes de elementos subordinados na família cujo `id` corresponde ao `WakefieldFamily`ordenadas por cidade.

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

Os exemplos anteriores mostram vários aspectos da linguagem de consulta do Cosmos DB:  

* Uma vez que a API de SQL funciona em valores JSON, ele lida com entidades em forma de árvore, em vez de linhas e colunas. Pode consultar os nós da árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem`, semelhante a referência de duas partes de `<table>.<column>` em ANSI SQL.

* Como funciona a linguagem de consulta com os dados sem esquemas, o sistema de tipo deve ser vinculado dinamicamente. A mesma expressão poderia resultar em diferentes tipos nos itens diferentes. O resultado de uma consulta é um valor JSON válido, mas não é garantido para ser de um esquema fixo.  

* Azure Cosmos DB suporta apenas os itens JSON rigorosos. O sistema de tipos e as expressões são restritas lidar apenas com os tipos JSON. Para obter mais informações, consulte a [especificação do JSON](https://www.json.org/).  

* Um contentor do Cosmos DB é uma coleção de esquemas de mensagens em fila de itens JSON. As relações de dentro e entre itens de contentor são implicitamente capturadas por contenção, não pela chave primária e relações de chave estrangeiras. Esta funcionalidade é importante para as junções de intra-item, abordadas posteriormente neste artigo.

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Cláusula SELECT](sql-query-select.md)
