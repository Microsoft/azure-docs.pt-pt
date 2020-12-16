---
title: SQL para Azure Monitor consulta de conta de contação folha de batota / Microsoft Docs
description: Ajuda para utilizadores familiarizados com SQL na escrita de consultas de log no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: e9f937323f4e14b5c8c3f30c94bf9d2daef0baea
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606696"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL para Azure Monitor consulta de conta de contação folha de batota 

A tabela abaixo ajuda os utilizadores que estão familiarizados com o SQL a aprender o idioma de consulta kusto para escrever consultas de log no Azure Monitor. Veja o comando T-SQL para resolver cenários comuns e o equivalente numa consulta de log do Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL para Azure Monitor

Descrição |Consulta SQL | Consulta de registo do Monitor de Azure
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Selecione todos os dados de uma tabela |`SELECT * FROM dependencies` |<code>dependencies</code>
Selecione colunas específicas de uma tabela |`SELECT name, resultCode FROM dependencies` |<code>dependencies <br>&#124; project name, resultCode</code>
Selecione 100 registos de uma mesa |`SELECT TOP 100 * FROM dependencies` |<code>dependencies <br>&#124; take 100</code>
Avaliação nulo |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL` |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Comparação de cordas: igualdade |`SELECT * FROM dependencies WHERE name = "abcde"` |<code>dependencies <br>&#124; where name == "abcde"</code>
Comparação de cordas: substring |`SELECT * FROM dependencies WHERE name like "%bcd%"` |<code>dependencies <br>&#124; where name contains "bcd"</code>
Comparação de cordas: wildcard |`SELECT * FROM dependencies WHERE name like "abc%"` |<code>dependencies <br>&#124; where name startswith "abc"</code>
Comparação de datas: último 1 dia |`SELECT * FROM dependencies WHERE timestamp > getdate()-1` |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Comparação de datas: intervalo de datas |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'` |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Comparação booleana |`SELECT * FROM dependencies WHERE !(success)` |<code>dependencies <br>&#124; where success == "False" </code>
Ordenar |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc` |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct |`SELECT DISTINCT name, type  FROM dependencies` |<code>dependencies <br>&#124; summarize by name, type </code>
Agrupamento, agregação |`SELECT name, AVG(duration) FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Pseudónimos da coluna, Estender |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Top n registos por medida |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc` |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
União |`SELECT * FROM dependencies UNION SELECT * FROM exceptions` |<code>union dependencies, exceptions</code>
União: com condições |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5` |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Associar |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Passos seguintes

- Ver as lições sobre [a escrita de consultas de registo no Azure Monitor](get-started-queries.md).
