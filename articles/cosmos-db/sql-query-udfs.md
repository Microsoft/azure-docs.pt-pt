---
title: Funções definidas pelo utilizador (UDFs) em Azure Cosmos DB
description: Conheça as funções definidas pelo Utilizador no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614327"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funções definidas pelo utilizador (UDFs) em Azure Cosmos DB

A API SQL fornece suporte para funções definidas pelo utilizador (UDFs). Com UDFs escalar, você pode passar em zero ou muitos argumentos e devolver um único resultado de argumento. A API verifica cada argumento por serem valores legais da JSON.  

A API alarga a sintaxe SQL para apoiar a lógica de aplicação personalizada usando UDFs. Pode registar UDFs com a API SQL e remeti-las em consultas SQL. Na verdade, os UDFs são requintadamente projetados para ligar de consultas. Como corolário, os UDFs não têm acesso ao objeto de contexto como outros tipos javaScript, tais como procedimentos armazenados e gatilhos. As consultas são apenas de leitura, e podem ser executadas em réplicas primárias ou secundárias. Os UDFs, ao contrário de outros tipos javaScript, são projetados para executar em réplicas secundárias.

O exemplo seguinte regista uma UDF sob um recipiente de artigos na base de dados cosmos. O exemplo cria uma UDF cujo nome é `REGEX_MATCH`. Aceita dois valores de cordas `input` `pattern`JSON e, e verifica se o primeiro corresponde `string.match()` ao padrão especificado no segundo utilizando a função javaScript.

## <a name="examples"></a>Exemplos

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