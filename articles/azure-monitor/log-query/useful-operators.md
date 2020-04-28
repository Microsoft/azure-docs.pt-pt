---
title: Operadores úteis em consultas de registo do Monitor Azure / Microsoft Docs
description: Funções comuns para usar para diferentes cenários em consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75397713"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Operadores úteis em consultas de registo do Monitor Azure

A tabela abaixo fornece algumas funções comuns para usar para diferentes cenários em consultas de registo do Monitor Azure.

## <a name="useful-operators"></a>Operadores úteis

Categoria                                |Função Análise Relevante
----------------------------------------|----------------------------------------
Seleção e pseudónimos da Coluna            |`project`, `project-away`, `extend`
Mesas e constantes temporárias          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Comparação e Operadores de Cordas         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Funções comuns de cordas                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Funções matemáticas comuns                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Texto de análise                            |`extract()`, `extractjson()`, `parse`, `split()`
Limitação da produção                         |`take`, `limit`, `top`, `sample`
Funções de data                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Agrupamento e agregação                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Adesão e Sindicatos                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Ordenar, ordenar                             |`sort`, `order` 
Objeto dinâmico (JSON e matriz)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Operadores lógicos                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Aprendizagem automática                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Passos seguintes

- Passe por uma lição sobre as consultas de registo de [escrita no Monitor Azure.](get-started-queries.md)
