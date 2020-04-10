---
title: Funções definidas pelo utilizador (UDFs) em Azure Cosmos DB
description: Conheça as funções definidas pelo Utilizador no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011128"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funções definidas pelo utilizador (UDFs) em Azure Cosmos DB

A API SQL fornece suporte para funções definidas pelo utilizador (UDFs). Com UDFs escalar, você pode passar em zero ou muitos argumentos e devolver um único resultado de argumento. A API verifica cada argumento por serem valores legais da JSON.  

## <a name="udf-use-cases"></a>UDF usa casos

A API alarga a sintaxe SQL para apoiar a lógica de aplicação personalizada usando UDFs. Pode registar UDFs com a API SQL e remeti-las em consultas SQL. Ao contrário dos procedimentos e gatilhos armazenados, os UDFs são apenas de leitura.

Usando UDFs, você pode estender a linguagem de consulta do Azure Cosmos DB. Os UDFs são uma ótima maneira de expressar uma lógica de negócio complexa na projeção de uma consulta.

No entanto, recomendamos evitar UDFs quando:

- Já existe uma [função](sql-query-system-functions.md) de sistema equivalente no Azure Cosmos DB. As funções do sistema usarão sempre menos RU's do que a UDF equivalente.
- A UDF é o `WHERE` único filtro na cláusula da sua consulta. A UDF's não utiliza o índice, pelo que avaliar a UDF exigirá documentos de carregamento. A combinação de predicados de filtro adicionais que utilizam `WHERE` o índice, em combinação com uma UDF, na cláusula reduzirá o número de documentos processados pela UDF.

Se tiver de utilizar a mesma UDF várias vezes numa consulta, deve fazer referência à UDF num [subquário,](sql-query-subquery.md#evaluate-once-and-reference-many-times)permitindo-lhe utilizar uma expressão JOIN para avaliar a UDF uma vez, mas referencia-la muitas vezes.

## <a name="examples"></a>Exemplos

O exemplo seguinte regista uma UDF sob um recipiente de artigos na base de dados cosmos. O exemplo cria uma UDF cujo nome é `REGEX_MATCH`. Aceita dois valores de cordas `input` `pattern`JSON e, e verifica se o primeiro corresponde `string.match()` ao padrão especificado no segundo utilizando a função javaScript.

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

Agora, use esta UDF numa projeção de consulta. Deve qualificar os UDFs com o `udf.` prefixo sensível ao caso quando os ligar de dentro de consultas.

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

Pode utilizar a UDF `udf.` qualificada com o prefixo dentro de um filtro, como no seguinte exemplo:

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

No essencial, os UDFs são expressões escalar válidas que pode utilizar tanto em projeções como em filtros.

Para expandir o poder dos UDFs, veja outro exemplo com lógica condicional:

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

O exemplo seguinte exerce a UDF:

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

Se as propriedades referidas pelos parâmetros da UDF não estiverem disponíveis no valor JSON, o parâmetro é considerado indefinido e a invocação da UDF é ignorada. Da mesma forma, se o resultado da UDF não for definido, não está incluído no resultado.

Como os exemplos anteriores mostram, os UDFs integram o poder da linguagem JavaScript com a API SQL. Os UDFs fornecem uma rica interface programável para fazer uma lógica processual complexa e condicional com a ajuda de capacidades de execução javaScript incorporadas. A API SQL fornece os argumentos aos UDFs para cada item de origem na atual fase de processamento da cláusula WHERE ou SELECT. O resultado é incorpore-se perfeitamente no gasoduto de execução global. Em resumo, os UDFs são ótimas ferramentas para fazer uma lógica de negócio complexa como parte de consultas.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de sistema](sql-query-system-functions.md)
- [Agregados](sql-query-aggregates.md)