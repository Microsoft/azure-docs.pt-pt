---
title: Funções do sistema na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre as funções do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 98a087f02035545b828cfcf11c58d03d02a5e76a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349185"
---
# <a name="system-functions-azure-cosmos-db"></a>Funções do sistema (Azure Cosmos DB)

 O cosmos DB fornece muitas funções internas do SQL. As categorias de funções internas são listadas abaixo.  
  
|Grupo de função|Descrição|Operações|  
|--------------|-----------------|-----------------| 
|[Funções de matriz](sql-query-array-functions.md)|As funções de matriz executam uma operação num valor de entrada de matriz e o valor de matriz, booleano ou numérico de retorno. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funções de data e hora](sql-query-date-time-functions.md)|As funções de data e hora permitem que você obtenha a data e a hora UTC atuais em duas formas; um carimbo de data/hora numérico cujo valor é a época do UNIX em milissegundos ou como uma cadeia de caracteres que está de acordo com o formato ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Funções matemáticas](sql-query-mathematical-functions.md)|As funções matemáticas realizar um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos e devolvem um valor numérico. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASEN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [TETO](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [GRAUS](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [POTÊNCIA](sql-query-power.md), [RADIANOS](sql-query-radians.md), [ALEATÓRIOS](sql-query-rand.md), [ARREDONDADOS](sql-query-round.md), [ASSINAR](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [QUADRADO](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Funções espaciais](sql-query-spatial-functions.md)|As funções geográficos realizar uma operação num valor de entrada do objeto espacial e retornam um valor numérico ou booleano. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Funções de cadeia](sql-query-string-functions.md)|As funções de cadeia de caracteres realizar uma operação num valor de entrada de cadeia de caracteres e retornam uma cadeia de caracteres, o valor numérico ou booleano. | [concat](sql-query-concat.md), [Contains](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [esquerda](sql-query-left.md), [comprimento](sql-query-length.md), [menor](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [substituir](sql-query-replace.md), [replicar](sql-query-replicate.md), [reverter](sql-query-reverse.md), [direita](sql-query-right.md), [RTrim](sql-query-rtrim.md), [ STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [substring](sql-query-substring.md), [ToString](sql-query-tostring.md), [arrumar](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[As funções de verificação de tipo](sql-query-type-checking-functions.md)|As funções de verificação de tipo permitem-lhe verificar o tipo de uma expressão dentro de consultas SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Funções internas versus definidas pelo usuário (UDFs)

Se você estiver usando uma UDF (função definida pelo usuário) para a qual uma função interna agora está disponível, a função interna correspondente será mais rápida de executar e mais eficiente.

## <a name="built-in-versus-ansi-sql-functions"></a>Funções SQL internas versus ANSI

A principal diferença entre Cosmos DB funções e funções ANSI SQL é que Cosmos DB funções são projetadas para funcionar bem com dados de esquema misto e sem esquema. Por exemplo, se uma propriedade estiver ausente ou tiver um valor não numérico como `unknown`, o item será ignorado em vez de retornar um erro.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de matriz](sql-query-array-functions.md)
- [Funções de data e hora](sql-query-date-time-functions.md)
- [Funções matemáticas](sql-query-mathematical-functions.md)
- [Funções espaciais](sql-query-spatial-functions.md)
- [Funções de cadeia](sql-query-string-functions.md)
- [As funções de verificação de tipo](sql-query-type-checking-functions.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregates.md)
