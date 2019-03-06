---
title: SQL para referência rápida do Azure Monitor log consulta | Documentos da Microsoft
description: Ajuda para os utilizadores familiarizados com SQL na gravação de consultas de registo no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 6a63ddc6b9a1640c8e0986a221c8da6f694b2800
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454141"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL para referência rápida do Azure Monitor log consulta 

A tabela abaixo ajuda os utilizadores que estão familiarizados com o SQL para aprender a linguagem de consulta de Kusto escrever consultas de registo no Azure Monitor. Dê uma olhada no comando T-SQL para resolver um cenários comuns e o equivalente numa consulta de registo do Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL para o Azure Monitor

Descrição                             |Consulta SQL                                                                                          |Consulta de registo do Azure Monitor
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
N registros Top pela medida                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
União                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
União: com condições                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions value < 5`              |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Associar                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Passos Seguintes

- Sempre o processo de uma aula sobre o [escrever consultas de registo no Azure Monitor](get-started-queries.md).