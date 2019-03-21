---
title: Otimizar a sua cache de geração 2 | Documentos da Microsoft
description: Saiba como monitorizar a cache de geração 2 no portal do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 26791aecb2ca57b31358d3385d07230c73c84904
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903876"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Como monitorizar a cache de geração 2
A arquitetura de armazenamento de geração 2 nivela automaticamente seus segmentos de columnstore consultados com mais frequência numa cache que reside no NVMe baseada em SSD concebida para armazéns de dados de geração 2. Melhor desempenho é percebido quando suas consultas obtêm segmentos são que residem na cache. Este artigo descreve como monitorizar e resolver problemas de desempenho de consulta lenta por determinar se a carga de trabalho ideal está aproveitando o cache de geração 2.  
## <a name="troubleshoot-using-the-azure-portal"></a>Resolver problemas com o portal do Azure
Pode utilizar o Azure Monitor para visualizar as métricas da cache de geração 2 para resolver problemas de desempenho de consulta. Primeiro, aceda ao portal do Azure e clique no Monitor:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Selecione o botão de métricas e preencha a **subscrição**, **recurso** **grupo**, **tipo de recurso**, e **recursos nome** do seu armazém de dados.

As principais métricas para resolução de problemas a cache de geração 2 são **porcentagem de acertos na Cache** e **Cache utilizada percentagem**. Configure o gráfico de métricas do Azure para exibir essas duas métricas.

![Métricas da cache](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Cache de acessos e utilizado de percentagem
A matriz a seguir descreve cenários com base nos valores das métricas de cache:

|                                | **Percentagem de acerto na Cache elevada** | **Percentagem de acerto na Cache de baixa** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Elevada percentagem de Cache utilizada** |          Cenário 1           |          Cenário 2          |
| **Percentagem de baixa de Cache utilizada**  |          Cenário 3           |          Cenário de 4          |

**Cenário 1:** Idealmente está a utilizar a cache. [Resolver problemas de](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) outras áreas que podem ser lentos suas consultas.

**Cenário 2:** Seu conjunto de dados de trabalho atual não pode caber no cache que faz com que uma baixa percentagem devido a leituras físicas de acertos na cache. Considere aumentar seu nível de desempenho e volte a executar a carga de trabalho para povoar a cache.

**Cenário 3:** É provável que a consulta está em execução lenta por razões não relacionado com a cache. [Resolver problemas de](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) outras áreas que podem ser lentos suas consultas. Também pode considerar [reduzir verticalmente a sua instância](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) para reduzir o tamanho da cache para reduzir os custos. 

**Cenário 4:** Tinha uma cache de fria que poderia ser o motivo por que a consulta foi lenta. Considere a reexecução sua consulta, como o conjunto de dados de trabalho deve agora ser em armazenado em cache. 

**Importante: Se a cache de acessos percentagem ou percentagem utilizada de cache não está a ser atualizada depois de executar novamente a sua carga de trabalho, o conjunto de trabalho pode já estar que reside na memória. Tenha em atenção columnstore em cluster apenas tabelas são colocadas em cache.**

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre ajuste de desempenho de consulta geral, consulte [monitorizar a execução da consulta](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


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
