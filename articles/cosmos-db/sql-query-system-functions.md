---
title: Funciona o sistema na linguagem de consulta DB do Azure Cosmos
description: Saiba mais sobre as funções do sistema SQL embutida e definida pelo utilizador no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b8883d7040ad5c73a376838f7cb1aaa1b558bfbd
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340838"
---
# <a name="system-functions-azure-cosmos-db"></a>Funções do sistema (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Cosmos DB fornece muitas funções SQL incorporadas. As categorias de funções incorporadas estão listadas abaixo.  
  
|Grupo de funções|Descrição|Operações|  
|--------------|-----------------|-----------------| 
|[Funções de matriz](sql-query-array-functions.md)|As funções de matriz executam uma operação sobre um valor de entrada de matriz e valor numérico de retorno, Boolean ou matriz. | [ARRAY_CONCAT,](sql-query-array-concat.md) [ARRAY_CONTAINS,](sql-query-array-contains.md) [ARRAY_LENGTH,](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funções de data e hora](sql-query-date-time-functions.md)|As funções de data e hora permitem obter a data e hora atuais da UTC em duas formas; um timetamp numérico cujo valor é a época Unix em milissegundos ou como uma corda que está em conformidade com o formato ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Funções matemáticas](sql-query-mathematical-functions.md)|As funções matemáticas realizam cada um cálculo, geralmente baseado em valores de entrada que são fornecidos como argumentos, e devolvem um valor numérico. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [TETO](sql-query-ceiling.md), [COS](sql-query-cos.md), [BERÇO](sql-query-cot.md), [GRAUS](sql-query-degrees.md), [EXP](sql-query-exp.md), [PISO](sql-query-floor.md), [LOG](sql-query-log.md), [LOG, LOG10](sql-query-log10.md), [PI,](sql-query-pi.md) [POWER,](sql-query-power.md) [RADIANS](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN,](sql-query-sign.md) [Sin,](sql-query-sin.md) [SQRT,](sql-query-sqrt.md) [SQUARE,](sql-query-square.md) [TAN,](sql-query-tan.md) [TRUNC](sql-query-trunc.md) |
|[Funções espaciais](sql-query-spatial-functions.md)|As funções espaciais executam uma operação sobre o valor de entrada de objetos espaciais e devolvem um valor numérico ou booleano. | [ST_DISTANCE,](sql-query-st-distance.md) [ST_INTERSECTS,](sql-query-st-intersects.md) [ST_ISVALID,](sql-query-st-isvalid.md) [ST_ISVALIDDETAILED,](sql-query-st-isvaliddetailed.md) [ST_WITHIN](sql-query-st-within.md) |
|[Funções de cadeia](sql-query-string-functions.md)|As funções de corda executam uma operação sobre um valor de entrada de corda e devolvem um valor de corda, numérico ou booleano. | [CONCA](sql-query-concat.md), [CONTAINS](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [ESQUERDA](sql-query-left.md), [COMPRIMENTO](sql-query-length.md), [INFERIOR](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[SUBSTITU,](sql-query-replace.md) [REPLICA,](sql-query-replicate.md) [REVERSO,](sql-query-reverse.md) [DIREITO,](sql-query-right.md) [RTRIM,](sql-query-rtrim.md) [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean,](sql-query-stringtoboolean.md) [StringTonull,](sql-query-stringtonull.md) [StringToNumber,](sql-query-stringtonumber.md) [StringToObject,](sql-query-stringtoobject.md) [Substring](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[Funções de verificação de tipo](sql-query-type-checking-functions.md)|As funções de verificação de tipo permitem verificar o tipo de expressão dentro das consultas SQL. | [IS_ARRAY,](sql-query-is-array.md) [IS_BOOL,](sql-query-is-bool.md) [IS_DEFINED,](sql-query-is-defined.md) [IS_NULL,](sql-query-is-null.md) [IS_NUMBER,](sql-query-is-number.md) [IS_OBJECT IS_PRIMITIVE](sql-query-is-object.md)IS_PRIMITIVE, [IS_STRING](sql-query-is-primitive.md) [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Funções definidas embutidos contra utilizadores (UDFs)

Se estiver atualmente a utilizar uma função definida pelo utilizador (UDF) para a qual está agora disponível uma função incorporada, a função incorporada correspondente será mais rápida e eficiente.

## <a name="built-in-versus-ansi-sql-functions"></a>Funções incorporadas versus ANSI SQL

A principal diferença entre as funções de Cosmos DB e as funções ANSI SQL é que as funções de Cosmos DB são projetadas para funcionar bem com dados de esquemas e esquemas mistos. Por exemplo, se uma propriedade estiver em falta ou tiver um valor não numérico `unknown` como, o item é ignorado em vez de devolver um erro.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de matriz](sql-query-array-functions.md)
- [Funções de data e hora](sql-query-date-time-functions.md)
- [Funções matemáticas](sql-query-mathematical-functions.md)
- [Funções espaciais](sql-query-spatial-functions.md)
- [Funções de cadeia](sql-query-string-functions.md)
- [Funções de verificação de tipo](sql-query-type-checking-functions.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)
