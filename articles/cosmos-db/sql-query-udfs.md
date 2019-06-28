---
title: Definido pelo utilizador funções (UDFs) no Azure Cosmos DB
description: Saiba mais sobre as funções definidas pelo utilizador no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342820"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Definido pelo utilizador funções (UDFs) no Azure Cosmos DB

A API de SQL fornece suporte para as funções definidas pelo utilizador (UDFs). Com UDFs escalares, pode passar argumentos de zero ou vários e devolver um resultado único argumento. A API verifica cada argumento por ser valores legais de JSON.  

A API estende a sintaxe do SQL Server para suportar a lógica de aplicativo personalizada com UDFs. Pode registar UDFs com a API de SQL e referenciá-los em consultas SQL. Na verdade, a UDFs exquisitely foram concebida para chamar a partir de consultas. Como resultado, UDFs não tem acesso ao objeto de contexto, como outros tipos de JavaScript, como procedimentos armazenados e acionadores. Consultas são só de leitura e podem ser executada em réplicas primárias ou secundárias. UDFs, ao contrário de outros tipos de JavaScript, foram concebidos para serem executadas em réplicas secundárias.

O exemplo a seguir registra uma UDF num contêiner de item na base de dados do Cosmos DB. O exemplo cria uma UDF cujo nome é `REGEX_MATCH`. Ela aceita dois valores de cadeia de caracteres do JSON, `input` e `pattern`, e verifica se as correspondências primeiro o padrão especificado na segunda através do JavaScript `string.match()` função.

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

Agora, utilize este UDF uma projeção da consulta. Tem qualificar UDFs com o prefixo de maiúsculas e minúsculas `udf.` ao chamá-los a partir de dentro de consultas.

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

Pode usar a UDF qualificada com o `udf.` prefixo dentro de um filtro, como no exemplo seguinte:

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

Em essência, UDFs são expressões escalares válidas que pode usar em projeções e filtros.

Para expandir o poder do UDFs, veja outro exemplo com lógica condicional:

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

O exemplo seguinte executa a UDF:

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

Se as propriedades referidas pela UDF não estão disponíveis no valor JSON parâmetros, o parâmetro é considerado como não definida e a invocação de UDF é ignorada. Da mesma forma, se o resultado do UDF não está definido, ele não está incluído no resultado.

Como mostram exemplos anteriores, UDFs integram o poder da linguagem JavaScript com a API de SQL. UDFs fornecem uma interface avançada programável fazer lógica complexa de procedimento, condicional com a ajuda de recursos internos de tempo de execução de JavaScript. A API de SQL fornece os argumentos para a UDFs para cada item de origem na cláusula SELECT ou onde atual estágio de processamento. O resultado é diretamente incorporado no pipeline de execução geral. Em resumo, UDFs são ferramentas excelentes para fazer a lógica de negócio complexa como parte das consultas.

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de sistema](sql-query-system-functions.md)
- [Agregados](sql-query-aggregates.md)