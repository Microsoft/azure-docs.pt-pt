---
title: Otimizar o cache Gen2
description: Saiba como monitorar o cache Gen2 usando o portal do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b33f7cedca4ef130eefa28c1dbaaedd82d11a9e4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645775"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Como monitorar o cache Gen2
A arquitetura de armazenamento Gen2 automaticamente coloca em camadas os segmentos columnstore consultados com mais frequência em um cache que reside em SSDs com base em NVMe projetado para Gen2 data warehouses. Um desempenho maior é percebido quando suas consultas recuperam segmentos que residem no cache. Este artigo descreve como monitorar e solucionar problemas de desempenho de consulta lento determinando se sua carga de trabalho está aproveitando o cache Gen2 de forma ideal.  
## <a name="troubleshoot-using-the-azure-portal"></a>Solucionar problemas usando o portal do Azure
Você pode usar Azure Monitor para exibir as métricas de cache Gen2 para solucionar problemas de desempenho de consulta. Primeiro, acesse a portal do Azure e clique em monitorar:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Selecione o botão métricas e preencha a **assinatura**, o **grupo**de recursos, o **tipo de recurso**e o **nome do recurso** do seu data warehouse.

As principais métricas para a solução de problemas do cache Gen2 são **percentual de impacto de cache** e percentual de **cache usado**. Configure o gráfico de métrica do Azure para exibir essas duas métricas.

![Métricas de cache](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Percentual de impacto de cache e usado
A matriz a seguir descreve os cenários com base nos valores das métricas de cache:

|                                | **Porcentagem alta de sucesso de cache** | **Percentual de impacto de cache baixo** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Percentual de alto cache usado** |          Cenário 1           |          Cenário 2          |
| **Percentual de uso de cache baixo**  |          Cenário 3           |          Cenário 4          |

**Cenário 1:** Você está usando o cache de forma ideal. [Solucione](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) outras áreas que podem estar atrasando suas consultas.

**Cenário 2:** O conjunto de dados de trabalho atual não pode se ajustar ao cache, o que causa uma baixa porcentagem de acesso ao cache devido a leituras físicas. Considere escalar verticalmente o nível de desempenho e executar novamente a carga de trabalho para preencher o cache.

**Cenário 3:** É provável que sua consulta esteja em execução lenta devido a motivos não relacionados ao cache. [Solucione](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) outras áreas que podem estar atrasando suas consultas. Você também pode considerar [o dimensionamento de sua instância](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) para reduzir o tamanho do cache para economizar custos. 

**Cenário 4:** Você tinha um cache frio que poderia ser o motivo pelo qual a consulta estava lenta. Considere a execução da sua consulta novamente, já que seu conjunto de trabalho em funcionamento deve estar armazenado em cache. 

**Importante: se a porcentagem de acesso ao cache ou o percentual de cache usado não estiver atualizando após a reexecução da carga de trabalho, seu conjunto de trabalhos já poderá estar residindo na memória. Observação somente as tabelas columnstore clusterizadas são armazenadas em cache.**

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o ajuste de desempenho de consulta geral, consulte [monitorar a execução da consulta](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
