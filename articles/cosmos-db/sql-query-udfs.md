---
title: UDFs (funções definidas pelo usuário) no Azure Cosmos DB
description: Saiba mais sobre as funções definidas pelo usuário no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614327"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>UDFs (funções definidas pelo usuário) no Azure Cosmos DB

A API do SQL fornece suporte para UDFs (funções definidas pelo usuário). Com UDFs escalares, você pode passar zero ou muitos argumentos e retornar um único resultado de argumento. A API verifica cada argumento para valores JSON válidos.  

A API estende a sintaxe SQL para dar suporte à lógica de aplicativo personalizada usando UDFs. Você pode registrar UDFs com a API do SQL e referenciá-los em consultas SQL. Na verdade, a UDFs exquisitely foram concebida para chamar a partir de consultas. Como um registro, os UDFs não têm acesso ao objeto de contexto como outros tipos de JavaScript, como procedimentos armazenados e gatilhos. As consultas são somente leitura e podem ser executadas em réplicas primárias ou secundárias. UDFs, ao contrário de outros tipos de JavaScript, são projetados para serem executados em réplicas secundárias.

O exemplo a seguir registra um UDF em um contêiner de item no banco de dados Cosmos. O exemplo cria um UDF cujo nome é `REGEX_MATCH`. Ele aceita dois valores `input` de cadeia de caracteres JSON e `pattern`verifica se o primeiro corresponde ao padrão especificado no segundo usando a função do `string.match()` JavaScript.

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

Agora, use essa UDF em uma projeção de consulta. Você deve qualificar UDFs com o prefixo `udf.` que diferencia maiúsculas de minúsculas ao chamá-los de dentro de consultas.

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

Você pode usar o UDF qualificado com o `udf.` prefixo dentro de um filtro, como no exemplo a seguir:

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

Em essência, as UDFs são expressões escalares válidas que você pode usar em projeções e filtros.

Para expandir o poder das UDFs, observe outro exemplo com a lógica condicional:

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

O exemplo a seguir exercita o UDF:

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

Se as propriedades referenciadas pelos parâmetros UDF não estiverem disponíveis no valor JSON, o parâmetro será considerado como indefinido e a invocação de UDF será ignorada. Da mesma forma, se o resultado do UDF for indefinido, ele não será incluído no resultado.

Como mostram os exemplos anteriores, as UDFs integram o poder da linguagem JavaScript com a API do SQL. As UDFs fornecem uma rica interface programável para realizar um procedimento complexo, lógica condicional, com a ajuda de recursos internos de tempo de execução do JavaScript. A API do SQL fornece os argumentos para as UDFs de cada item de origem no estágio atual de cláusula WHERE ou SELECT do processamento. O resultado é incorporado diretamente no pipeline de execução geral. Em resumo, as UDFs são ótimas ferramentas para fazer uma lógica de negócios complexa como parte das consultas.

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Agregações](sql-query-aggregates.md)