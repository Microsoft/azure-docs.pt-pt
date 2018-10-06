---
title: Operadores úteis em consultas do Log Analytics do Azure | Documentos da Microsoft
description: Funções comuns a utilizar para diferentes cenários em consultas do Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 0b14c13462f15dd90285ed9e37080487324a4d85
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831284"
---
# <a name="useful-operators-in-log-analytics-queries"></a>Operadores úteis em consultas do Log Analytics

A tabela abaixo fornece algumas funções comuns a utilizar para diferentes cenários em consultas do Log Analytics.

## <a name="useful-operators"></a>Operadores úteis

Categoria                                |Função de análise relevantes
----------------------------------------|----------------------------------------
Aliases de seleção e na coluna            |`project`, `project-away`, `extend`
Tabelas temporárias e constantes          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Comparação e operadores de seqüência de caracteres         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Funções de cadeia de caracteres comuns                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Funções de matemáticas comuns                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Análise de texto                            |`extract()`, `extractjson()`, `parse`, `split()`
Limitando a saída                         |`take`, `limit`, `top`, `sample`
Funções de data                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Agrupamento e a agregação                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Junções e uniões                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Ordenação, a ordem                             |`sort`, `order` 
Objeto dinâmico (JSON e matriz)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Operadores lógicos                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Aprendizagem automática                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Passos Seguintes

- Sempre o processo de uma aula sobre o [escrevem consultas no Log Analytics](get-started-queries.md).
