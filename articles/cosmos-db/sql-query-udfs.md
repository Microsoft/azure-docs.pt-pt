---
title: Funções definidas pelo utilizador (UDFs) em Azure Cosmos DB
description: Saiba mais sobre funções definidas pelo utilizador no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 98698264f0beb25a8b4f74861f1150ae889d7115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546343"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funções definidas pelo utilizador (UDFs) em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A API SQL fornece suporte para funções definidas pelo utilizador (UDFs). Com UDFs escalar, pode passar em zero ou muitos argumentos e devolver um único resultado de argumento. A API verifica cada argumento por ser valores JSON legais.  

## <a name="udf-use-cases"></a>Casos de utilização da UDF

A API estende a sintaxe SQL para suportar a lógica de aplicação personalizada usando UDFs. Pode registar UDFs com a API SQL e faz referência em consultas SQL. Ao contrário dos procedimentos e gatilhos armazenados, os UDFs são apenas de leitura.

Utilizando UDFs, pode estender a linguagem de consulta do Azure Cosmos DB. As UDFs são uma ótima maneira de expressar lógica de negócio complexa na projeção de uma consulta.

No entanto, recomendamos evitar os UDFs quando:

- Já existe [uma função](sql-query-system-functions.md) equivalente do sistema na Azure Cosmos DB. As funções do sistema utilizarão sempre menos RU's do que o equivalente uDF.
- O UDF é o único filtro na `WHERE` cláusula da sua consulta. Os UDF não utilizam o índice, pelo que a avaliação do UDF exigirá documentos de carregamento. Combinando predicados de filtros adicionais que usam o índice, em combinação com um UDF, na `WHERE` cláusula reduzirá o número de documentos processados pela UDF.

Se tiver de utilizar o mesmo UDF várias vezes numa consulta, deve fazer referência ao UDF num [subquery,](sql-query-subquery.md#evaluate-once-and-reference-many-times)permitindo-lhe utilizar uma expressão JOIN para avaliar o UDF uma vez, mas referencia-lo muitas vezes.

## <a name="examples"></a>Exemplos

O exemplo a seguir regista um UDF sob um recipiente de item na base de dados cosmos. O exemplo cria um UDF cujo nome é `REGEX_MATCH` . Aceita dois valores de cadeia JSON `input` e , e verifica se o primeiro corresponde ao `pattern` padrão especificado no segundo usando a função de `string.match()` JavaScript.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Agora, use este UDF numa projeção de consulta. Deve qualificar os UDFs com o prefixo sensível ao caso `udf.` quando os chamar de dentro de consultas.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Os resultados são:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Pode utilizar o UDF qualificado com o `udf.` prefixo dentro de um filtro, como no seguinte exemplo:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Os resultados são:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

No essencial, os UDFs são expressões escalaras válidas que pode usar tanto em projeções como em filtros.

Para expandir o poder das UDFs, veja outro exemplo com lógica condicional:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

O exemplo a seguir é o UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Os resultados são:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Se as propriedades referidas pelos parâmetros UDF não estiverem disponíveis no valor JSON, o parâmetro é considerado indefinido e a invocação UDF é ignorada. Da mesma forma, se o resultado do UDF for indefinido, não está incluído no resultado.

Como os exemplos anteriores mostram, os UDFs integram o poder da linguagem JavaScript com a API SQL. Os UDFs fornecem uma interface programável rica para fazer uma lógica processual complexa e condicional com a ajuda de capacidades de tempo de execução javaScript incorporadas. A API SQL fornece os argumentos aos UDFs para cada item de origem na fase atual da cláusula WHERE ou SELECT de processamento. O resultado está perfeitamente incorporado no gasoduto de execução global. Em resumo, as UDFs são ótimas ferramentas para fazer lógicas de negócio complexas como parte de consultas.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Agregados](sql-query-aggregate-functions.md)