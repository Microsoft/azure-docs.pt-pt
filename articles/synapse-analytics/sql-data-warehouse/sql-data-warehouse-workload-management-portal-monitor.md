---
title: Monitorização do portal de gestão da carga de trabalho
description: Orientação para monitorização do portal de gestão da carga de trabalho no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 04a3fa79a6940a5b7a4bb98d08aa8be48a442903
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728605"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics – Monitorização do Portal de Gestão da Carga de Trabalho

Este artigo explica como monitorizar a utilização e a atividade de consulta de recursos do grupo de carga de [trabalho.](sql-data-warehouse-workload-isolation.md#workload-groups)
Para mais detalhes sobre como configurar o Azure Metrics Explorer veja o Início com o artigo [do Azure Metrics Explorer.](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  Consulte a secção [de utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) na documentação de monitorização do Azure Synapse Analytics para obter mais informações sobre como monitorizar o consumo de recursos do sistema.
Existem duas categorias diferentes de métricas do grupo de trabalho previstas para monitorizar a gestão da carga de trabalho: alocação de recursos e atividade de consulta.  Estas métricas podem ser divididas e filtradas por grupo de carga de trabalho.  As métricas podem ser divididas e filtradas com base em se forem definidas pelo sistema (grupos de carga de trabalho da classe de recursos) ou definidas pelo utilizador (criadas pelo utilizador com sintaxe DO GRUPO DE CARGA DE CARGA de [TRABALHO).](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="workload-management-metric-definitions"></a>Definições métricas de gestão da carga de trabalho

|Nome da Métrica                    |Descrição  |Tipo de Agregação |
|-------------------------------|-------------|-----------------|
|Por cento efetivo do recurso cap | *A percentagem efetiva de recursos de capital* é um limite difícil para a percentagem de recursos acessíveis pelo grupo de carga de trabalho, tendo em conta a *percentagem efetiva de recursos min* atribuída a outros grupos de carga de trabalho. A métrica *de percentagem de recursos de tampa eficaz* é configurada utilizando o parâmetro na `CAP_PERCENTAGE_RESOURCE` sintaxe do GRUPO DE CARGA DE [CARGA CREATE.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  O valor efetivo é descrito aqui.<br><br>Por exemplo, se um grupo de carga de trabalho `DataLoads` for criado com = `CAP_PERCENTAGE_RESOURCE` 100 e outro grupo de carga de trabalho for criado com uma percentagem efetiva de recursos min de 25%, o *adicional de recursos de limite efetivo* para o grupo de carga de trabalho é de `DataLoads` 75%.<br><br>A *percentagem de recursos de tampa eficaz* determina o limite superior da concuência (e, portanto, a produção potencial) que um grupo de carga de trabalho pode alcançar.  Se for necessário um rendimento adicional para além do que é atualmente reportado pela métrica *de recursos de limites eficazes,* ou aumente `CAP_PERCENTAGE_RESOURCE` o, diminua o `MIN_PERCENTAGE_RESOURCE` número de outros grupos de carga de trabalho ou aumente o caso para adicionar mais recursos.  Diminuir a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode aumentar a conúnquidade, mas pode não aumentar a produção global.| Min, Avg, Max |
|Por cento de recursos min eficazes |*O min resource por cento efetivo* é a percentagem mínima de recursos reservados e isolados para o grupo de trabalho, tendo em conta o nível mínimo de serviço.  A métrica de percentagem de recursos min eficaz é configurada utilizando o `MIN_PERCENTAGE_RESOURCE` parâmetro na sintaxe [do GRUPO DE CARGA DE TRABALHO CREATE.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  O valor efetivo é descrito [aqui.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values)<br><br>Utilize o tipo de agregação de Soma quando esta métrica não estiver filtrada e não for executada para monitorizar o isolamento total da carga de trabalho configurado no sistema.<br><br>O *min 000* de recursos eficazes determina o limite inferior de garantia de concuência (e, portanto, produção garantida) que um grupo de carga de trabalho pode alcançar.  Se forem necessários recursos garantidos adicionais para além do que é atualmente reportado pela métrica *de recursos min eficazes,* aumente o `MIN_PERCENTAGE_RESOURCE` parâmetro configurado para o grupo de carga de trabalho.  Diminuir a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode aumentar a conúnquidade, mas pode não aumentar a produção global. |Min, Avg, Max|
|Consultas ativas do grupo de carga de trabalho  |Esta métrica relata as consultas ativas dentro do grupo de carga de trabalho.  A utilização desta métrica não filtrada e não filtrada exibe todas as consultas ativas em execução no sistema.|Soma         |
|Alocação de grupo de carga de trabalho por percentagem máxima de recursos |Esta métrica apresenta a percentagem de alocação de recursos em relação à *percentagem de recursos de limite efetivo* por grupo de carga de trabalho.  Esta métrica proporciona a utilização eficaz do grupo de carga de trabalho.<br><br>Considere um grupo de carga de trabalho `DataLoads` com uma *percentagem de recursos de limite eficaz* de 75% e um `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurado a 25%.  A *dotação do grupo Workload por* valor máximo de recursos por cento filtrado `DataLoads` seria de 33% (25% / 75%) se uma única consulta estivesse a decorrer neste grupo de carga de trabalho.<br><br>Use esta métrica para identificar a utilização de um grupo de carga de trabalho.  Um valor próximo de 100% indica que todos os recursos disponíveis para o grupo de carga de trabalho estão a ser utilizados.  Além disso, o *grupo workload fez fila* para o mesmo grupo de trabalho que mostra um valor superior a zero indica que o grupo de carga de trabalho utilizaria recursos adicionais se fosse atribuído.  Inversamente, se esta métrica for consistentemente baixa e o *grupo de trabalho de trabalho as consultas ativas* forem baixas, o grupo de carga de trabalho não está a ser utilizado.  Esta situação é especialmente problemática se *a percentagem de recursos de limite eficaz* for superior a zero, uma vez que isso indica um isolamento de carga de trabalho [subaproveitada.](#underutilized-workload-isolation)|Min, Avg, Max |
|Alocação de grupo de carga de trabalho por parte do sistema | Esta métrica apresenta a percentagem de alocação de recursos em relação a todo o sistema.<br><br>Considere um grupo de trabalho `DataLoads` com uma `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configuração de 25%.  *A atribuição de grupos de carga de trabalho por* valor por cento do sistema filtrado seria de `DataLoads` 25% (25% / 100%) se uma única consulta estivesse a decorrer neste grupo de carga de trabalho.|Min, Avg, Max |
|Tempo limites de consulta do grupo de carga de trabalho |Consultas para o grupo de trabalho que tem cronometrado.  Os intervalos de consulta reportados por esta métrica são apenas uma vez que a consulta tenha começado a executar (não inclui o tempo de espera devido ao bloqueio ou espera de recursos).<br><br>O tempo de consulta é configurado utilizando o `QUERY_EXECUTION_TIMEOUT_SEC` parâmetro na sintaxe [do GRUPO DE CARGA CREATE.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  Aumentar o valor poderia reduzir o número de intervalos de consulta.<br><br>Considere aumentar o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parâmetro para o grupo de carga de trabalho reduzir a quantidade de intervalos de tempo e alocar mais recursos por consulta.  Nota: o aumento `REQUEST_MIN_RESOURCE_GRANT_PERCENT` reduz a conusão para o grupo de trabalho. |Soma |
|Consultas em fila de grupos de carga | Consultas para o grupo de trabalho que está atualmente em fila à espera de iniciar a execução.  As consultas podem ser na fila porque estão à espera de recursos ou fechaduras.<br><br>As consultas podem estar à espera de inúmeras razões.  Se o sistema estiver sobrecarregado e a procura de concordância for maior do que a disponível, as consultas irão fazer fila.<br><br>Considere adicionar mais recursos ao grupo de trabalho, aumentando o `CAP_PERCENTAGE_RESOURCE` parâmetro na declaração do GRUPO DE TRABALHO [CREATE.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  Se `CAP_PERCENTAGE_RESOURCE` for maior do que a métrica de recursos de limite *eficaz,* o isolamento configurado da carga de trabalho para outro grupo de carga de trabalho está a afetar os recursos atribuídos a este grupo de carga de trabalho.  Considere baixar `MIN_PERCENTAGE_RESOURCE` outros grupos de carga de trabalho ou aumentar o caso para adicionar mais recursos. |Soma |

## <a name="monitoring-scenarios-and-actions"></a>Acompanhamento de cenários e ações

Abaixo estão uma série de configurações de gráficos para destacar o uso da métrica de gestão da carga de trabalho para a resolução de problemas, juntamente com as ações associadas para resolver o problema.

### <a name="underutilized-workload-isolation"></a>Isolamento de carga de trabalho subutilizada

Considere a seguinte configuração do grupo de carga de trabalho e do classificador onde é criado um grupo de carga de trabalho `wgPriority` nomeado e *o TheCEO* `membername` é mapeado para ele usando o classificador de `wcCEOPriority` carga de trabalho.  O `wgPriority` grupo de carga de trabalho tem 25% de isolamento de carga de trabalho configurado para ele `MIN_PERCENTAGE_RESOURCE` (= 25).  Cada consulta submetida pela *TheCEO* recebe 5% dos recursos do sistema `REQUEST_MIN_RESOURCE_GRANT_PERCENT` (= 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

O gráfico abaixo é configurado da seguinte forma:<br>
Métrica 1: *Percentagem eficaz de recursos min* (agregação Avg, `blue line` )<br>
Métrica 2: *Alocação do grupo de carga de trabalho por percentagem* do sistema (agregação Avg, `purple line` )<br>
Filtro: [Grupo de Carga de Trabalho] = `wgPriority`<br>
![A screenshot mostra um gráfico com as duas métricas e filtro.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
O gráfico mostra que, com 25% de isolamento da carga de trabalho, apenas 10% está a ser usado em média.  Neste caso, o valor do `MIN_PERCENTAGE_RESOURCE` parâmetro poderia ser reduzido para entre 10 ou 15 e permitir que outras cargas de trabalho no sistema consumissem os recursos.

### <a name="workload-group-bottleneck"></a>Estrangulamento do grupo de carga de trabalho

Considere a seguinte configuração do grupo de carga de trabalho e do classificador onde um grupo de carga de trabalho nomeado `wgDataAnalyst` é criado e o *DataAnalyst* `membername` é mapeado para ele usando o classificador de `wcDataAnalyst` carga de trabalho.  O `wgDataAnalyst` grupo de trabalho tem 6% de isolamento de carga de trabalho configurado para ele `MIN_PERCENTAGE_RESOURCE` (= 6) e um limite de recursos de 9% `CAP_PERCENTAGE_RESOURCE` (= 9).  Cada consulta submetida pelo *DataAnalyst* recebe 3% dos recursos do sistema `REQUEST_MIN_RESOURCE_GRANT_PERCENT` (= 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

O gráfico abaixo é configurado da seguinte forma:<br>
Métrica 1: *Percentagem eficaz de recursos da tampa* (agregação Avg, `blue line` )<br>
Métrica 2: *Alocação do grupo de carga de trabalho por percentagem máxima* de recursos (agregação Avg, `purple line` )<br>
Métrica 3: Consultas em fila do *grupo de trabalho* (agregação de soma, `turquoise line` )<br>
Filtro: [Grupo de Carga de Trabalho] = `wgDataAnalyst`<br>
![A screenshot mostra um gráfico com as três métricas e o filtro.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
O gráfico mostra que, com um limite máximo de 9% sobre os recursos, o grupo de carga de trabalho é 90% utilizado (a partir da *alocação do grupo Workload por métrica de recursos máximos).*  Há uma fila constante de consultas, como mostra o *grupo workload que faz fila métrica*.  Neste caso, o aumento do `CAP_PERCENTAGE_RESOURCE` valor superior a 9% permitirá que mais consultas sejam executadas simultaneamente.  Aumentando as `CAP_PERCENTAGE_RESOURCE` assumimos que existem recursos suficientes disponíveis e não isolados por outros grupos de trabalho.  Verifique se a tampa aumentada verificando a *métrica de percentagem de recursos da tampa eficaz*.  Se se desejar mais produção, considere também aumentar o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` valor superior a 3.  Aumentar o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` poderia permitir que as consultas corressem mais rápido.

## <a name="next-steps"></a>Próximos passos

- [Quickstart: Configure o isolamento da carga de trabalho usando t-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [CRIAR GRUPO DE CARGA DE TRABALHO (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [CRIAR CLASSIFICAÇÃO DE CARGA DE TRABALHO (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Monitorização da utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md)
