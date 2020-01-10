---
title: Operadores úteis em consultas de log de Azure Monitor | Microsoft Docs
description: Funções comuns a serem usadas em cenários diferentes em consultas Azure Monitor log.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75397713"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Operadores úteis em consultas de log de Azure Monitor

A tabela a seguir fornece algumas funções comuns a serem usadas em cenários diferentes em consultas Azure Monitor log.

## <a name="useful-operators"></a>Operadores úteis

Categoria                                |Função de análise relevante
----------------------------------------|----------------------------------------
Aliases de seleção e coluna            |`project`, `project-away`, `extend`
Tabelas e constantes temporárias          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Comparação e operadores de cadeia de caracteres         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Funções de cadeia de caracteres comuns                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Funções matemáticas comuns                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Analisando texto                            |`extract()`, `extractjson()`, `parse`, `split()`
Limitando a saída                         |`take`, `limit`, `top`, `sample`
Funções de data                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Agrupamento e agregação                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Junções e uniões                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Classificar, ordem                             |`sort`, `order` 
Objeto dinâmico (JSON e matriz)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Operadores lógicos                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Machine learning                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Passos seguintes

- Percorra uma lição sobre as [consultas de log de gravação em Azure monitor](get-started-queries.md).
