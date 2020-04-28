---
title: Funciona mento do sistema na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre as funções do sistema SQL incorporado e do utilizador definido sQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74870535"
---
# <a name="system-functions-azure-cosmos-db"></a>Funções do sistema (Azure Cosmos DB)

 Cosmos DB fornece muitas funções SQL incorporadas. As categorias de funções incorporadas estão listadas abaixo.  
  
|Grupo de funções|Descrição|Operações|  
|--------------|-----------------|-----------------| 
|[Funções de matriz](sql-query-array-functions.md)|As funções de matriz executam uma operação com um valor de entrada de matriz e retorno numérico, booleano ou valor de matriz. | [ARRAY_CONCAT,](sql-query-array-concat.md) [ARRAY_CONTAINS,](sql-query-array-contains.md) [ARRAY_LENGTH, ARRAY_SLICE](sql-query-array-slice.md) [ARRAY_LENGTH](sql-query-array-length.md) |
|[Funções de data e hora](sql-query-date-time-functions.md)|As funções de data e hora permitem-lhe obter a data e hora utc atuais em duas formas; uma marca de tempo numérica cujo valor é a época Unix em milissegundos ou como uma cadeia que se conforma com o formato ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Funções matemáticas](sql-query-mathematical-functions.md)|As funções matemáticas realizam cada um um cálculo, geralmente com base em valores de entrada que são fornecidos como argumentos, e devolvem um valor numérico. | [ABS,](sql-query-abs.md) [ACOS,](sql-query-acos.md) [ASIN,](sql-query-asin.md) [ATAN,](sql-query-atan.md) [ATN2,](sql-query-atn2.md) [TETO,](sql-query-ceiling.md) [COS,](sql-query-cos.md) [BERÇO,](sql-query-cot.md) [GRAUS,](sql-query-degrees.md) [EXP,](sql-query-exp.md) [PISO,](sql-query-floor.md) [LOG,](sql-query-log.md) [LOG10,](sql-query-log10.md) [PI,](sql-query-pi.md) [POWER,](sql-query-power.md) [RADIANS,](sql-query-radians.md) [RAND,](sql-query-rand.md) [ROUND,](sql-query-round.md) [SIGN,](sql-query-sign.md) [SIN,](sql-query-sin.md) [SQRT,](sql-query-sqrt.md) [SQUARE,](sql-query-square.md) [TAN,](sql-query-tan.md) [TRUNC](sql-query-trunc.md) |
|[Funções espaciais](sql-query-spatial-functions.md)|As funções espaciais executam uma operação sobre um valor de entrada de objeto espacial e devolvem um valor numérico ou booleano. | [ST_DISTANCE,](sql-query-st-distance.md) [ST_INTERSECTS,](sql-query-st-intersects.md) [ST_ISVALID, ST_ISVALIDDETAILED,](sql-query-st-isvaliddetailed.md) [ST_WITHIN](sql-query-st-within.md) [ST_ISVALID](sql-query-st-isvalid.md) |
|[Funções de corda](sql-query-string-functions.md)|As funções de cadeia executam uma operação sobre um valor de entrada de cordas e devolvem um valor de cadeia, numérico ou booleano. | [CONCAT](sql-query-concat.md), [CONTÉMS,](sql-query-contains.md) [ENDSWITH,](sql-query-endswith.md) [INDEX_OF,](sql-query-index-of.md) [ESQUERDA,](sql-query-left.md) [COMPRIMENTO,](sql-query-length.md)PARTE [INFERIOR,](sql-query-lower.md) [LTRIM,](sql-query-ltrim.md) [SUBSTITUIÇÃO,](sql-query-replace.md) [REPLICAÇÃO](sql-query-replicate.md), [REVERSO,](sql-query-reverse.md) [DIREITA,](sql-query-right.md) [RTRIM,](sql-query-rtrim.md) [STARTSWITH,](sql-query-startswith.md) [TRIM](sql-query-trim.md) [StringToArray,](sql-query-stringtoarray.md) [StringToBoolean,](sql-query-stringtoboolean.md) [StringToNull,](sql-query-stringtonull.md) [StringToNumber,](sql-query-stringtonumber.md) [StringToObject,](sql-query-stringtoobject.md) [SUBSTRING,](sql-query-substring.md) [ToString, TRIM,](sql-query-tostring.md) [UPPER](sql-query-upper.md) |
|[Funções de verificação de tipo](sql-query-type-checking-functions.md)|As funções de verificação de tipos permitem verificar o tipo de expressão dentro das consultas SQL. | [IS_ARRAY,](sql-query-is-array.md) [IS_BOOL,](sql-query-is-bool.md) [IS_DEFINED, IS_NULL IS_NUMBER,](sql-query-is-null.md) [IS_DEFINED](sql-query-is-defined.md) [IS_OBJECT,](sql-query-is-number.md) [IS_PRIMITIVE,](sql-query-is-primitive.md) [IS_STRING](sql-query-is-string.md) [IS_OBJECT](sql-query-is-object.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Funções definidas por utilizador (UDFs)

Se estiver a utilizar atualmente uma função definida pelo utilizador (UDF) para a qual está agora disponível uma função incorporada, a função incorporada correspondente será mais rápida e eficiente.

## <a name="built-in-versus-ansi-sql-functions"></a>Funções sql incorporadas versus ANSI

A principal diferença entre as funções Cosmos DB e AS funções ANSI SQL é que as funções Cosmos DB são projetadas para funcionar bem com dados schemaless e mixed-schema. Por exemplo, se falta uma propriedade ou se `unknown`tiver um valor não numérico como , o item é ignorado em vez de devolver um erro.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de matriz](sql-query-array-functions.md)
- [Funções de data e hora](sql-query-date-time-functions.md)
- [Funções matemáticas](sql-query-mathematical-functions.md)
- [Funções espaciais](sql-query-spatial-functions.md)
- [Funções de corda](sql-query-string-functions.md)
- [Funções de verificação de tipo](sql-query-type-checking-functions.md)
- [Funções Definidas pelo Utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)
