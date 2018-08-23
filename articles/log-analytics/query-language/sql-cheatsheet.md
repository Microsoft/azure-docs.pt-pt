---
title: SQL para referência rápida do Azure Log Analytics query language | Documentos da Microsoft
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
ms.openlocfilehash: 8cc05f02d97eac6bb24c4a0565df8c7335c5b33d
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42447524"
---
# <a name="sql-to-log-analytics-query-language-cheat-sheet"></a>SQL para a folha de cheat de linguagem de consulta do Log Analytics 

A tabela abaixo ajuda os utilizadores que estão familiarizados com o SQL para aprender a linguagem de consulta do Log Analytics. Dê uma olhada no comando T-SQL para resolver um cenários comuns e o equivalente a utilizar o Log Analytics.

## <a name="sql-to-log-analytics"></a>SQL para o Log Analytics

Descrição                             |Consulta SQL                                                                                          |Consulta de análise de registos do Azure
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Selecione todos os dados a partir de uma tabela            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Selecionar colunas específicas de uma tabela    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Selecionar 100 registos de uma tabela         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Avaliação nulo                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Comparação de cadeias de caracteres: igualdade             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Comparação de cadeias de caracteres: subcadeia            |`SELECT * FROM dependencies WHERE like "%bcd%"`                                                    |<code>dependencies <br>&#124; where name contains "bcd"</code>
Comparação de cadeias de caracteres: carateres universais             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Comparação de datas: último dia             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Comparação de datas: intervalo de datas             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Booleanos de comparação                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Ordenar                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Distintos                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Agrupamento, agregação                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Aliases de coluna, expandir                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Recrods n principais pela medida                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
União                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
União: com condições                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions value < 5`              |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Associar                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Passos Seguintes

- Sempre o processo de uma aula sobre o [escrevem consultas no Log Analytics](get-started-queries.md).