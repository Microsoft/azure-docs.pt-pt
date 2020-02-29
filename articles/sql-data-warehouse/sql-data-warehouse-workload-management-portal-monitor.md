---
title: Monitorização do portal de gestão da carga de trabalho
description: Orientação para monitorização do portal de gestão de carga de trabalho no Azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 69a200d4fda940f072960da9224f84a22db51647
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78193803"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – Monitorização do Portal de Gestão da Carga de Trabalho (Pré-visualização)
Este artigo explica como monitorizar a utilização de recursos do grupo de [trabalho](sql-data-warehouse-workload-isolation.md#workload-groups) e a atividade de consulta. Para mais detalhes sobre como configurar o Azure Metrics Explorer veja o Getting started com o artigo [do Azure Metrics Explorer.](../azure-monitor/platform/metrics-getting-started.md)  Consulte a secção de [utilização](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) de Recursos no Azure Synapse Analytics Analytics Documentação para obter detalhes sobre como monitorizar o consumo de recursos do sistema.
Existem duas categorias diferentes de métricas de grupo de carga de trabalho previstas para monitorizar a gestão da carga de trabalho: alocação de recursos e atividade de consulta.  Estas métricas podem ser divididas e filtradas pelo grupo de carga de trabalho.  As métricas podem ser divididas e filtradas com base no facto de serem definidas pelo sistema (grupos de carga de trabalho da classe de recursos) ou definidas pelo utilizador (criadas pelo utilizador com sintaxe [CREATE WORKLOAD GROUP).](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="workload-management-metric-definitions"></a>Definições métricas de gestão da carga de trabalho

|Nome da Métrica                    |Descrição  |Tipo de Agregação |
|-------------------------------|-------------|-----------------|
|Por cento de recursos de tampa eficazes | *A percentagem efetiva de recursos da TAMPA* é um limite difícil para a percentagem de recursos acessíveis pelo grupo de carga de trabalho, tendo em conta a *percentagem efetiva de recursos min* atribuídos a outros grupos de carga de trabalho. A métrica de por cento de *recursos da tampa eficaz* é configurada usando o parâmetro `CAP_PERCENTAGE_RESOURCE` na sintaxe CREATE WORKLOAD [GROUP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  O valor efetivo é descrito aqui.<br><br>Por exemplo, se um grupo de trabalho `DataLoads` for criado com `CAP_PERCENTAGE_RESOURCE` = 100 e outro grupo de carga de trabalho for criado com uma percentagem de recursos min eficaz de 25%, o *percenta de recursos efetivos* para o grupo de carga de trabalho `DataLoads` é de 75%.<br><br>O *% de recursos efetivos* da tampa determina o limite superior da moeda (e, portanto, o potencial de produção) que um grupo de carga de trabalho pode alcançar.  Se for necessário um acréscimo adicional para além do que é atualmente reportado pela métrica de percentagem de *recursos da tampa efetiva,* aumente a `CAP_PERCENTAGE_RESOURCE`, diminua a `MIN_PERCENTAGE_RESOURCE` de outros grupos de carga de trabalho ou aumente a instância para adicionar mais recursos.  A diminuição do `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode aumentar a conmoedação, mas pode não aumentar a sua percentagem global.| Min, Avg, Max |
|Por cento de recursos min eficazes |A percentagem efetiva dos *recursos min* é a percentagem mínima de recursos reservados e isolados para o grupo de carga de trabalho tendo em conta o nível mínimo de serviço.  A métrica efetiva do recurso min é configurada utilizando o parâmetro `MIN_PERCENTAGE_RESOURCE` na sintaxe [CREATE WORKLOAD GROUP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  O valor efetivo é descrito [aqui.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)<br><br>Utilize o tipo de agregação de soma quando esta métrica não for filtrada e não dividida para monitorizar o isolamento total da carga de trabalho configurado no sistema.<br><br>O *eficaz min resource por cento* determina o limite inferior da moeda garantida (e, portanto, a produção garantida) que um grupo de carga de trabalho pode alcançar.  Se forem necessários recursos adicionais garantidos para além do que é atualmente reportado pela métrica efetiva de *recursos min,* aumente o parâmetro `MIN_PERCENTAGE_RESOURCE` configurado para o grupo de carga de trabalho.  A diminuição do `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode aumentar a conmoedação, mas pode não aumentar a sua percentagem global. |Min, Avg, Max|
|Consultas ativas do grupo de trabalho  |Esta métrica relata as consultas ativas dentro do grupo de carga de trabalho.  A utilização desta métrica não filtrada e não dividida apresenta todas as consultas ativas em funcionamento no sistema.|Soma         |
|Atribuição do grupo de carga de trabalho por cento de recursos máximos |Esta métrica apresenta a repartição percentual de recursos em relação ao *recurso efetiva da tampa* por grupo de carga de trabalho.  Esta métrica proporciona a utilização eficaz do grupo de carga de trabalho.<br><br>Considere um grupo de carga de trabalho `DataLoads` com um *recurso de capital eficaz* de 75% e um `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurado em 25%.  A atribuição do *grupo Workload por um* valor máximo de recursos filtrado para `DataLoads` seria de 33% (25% / 75%) se uma única consulta estivesse a decorrer neste grupo de carga de trabalho.<br><br>Utilize esta métrica para identificar a utilização de um grupo de carga de trabalho.  Um valor próximo de 100% indica que todos os recursos disponíveis para o grupo de carga de trabalho estão a ser utilizados.  Além disso, a métrica de *consultas do grupo workload* para o mesmo grupo de carga de trabalho que mostrasse um valor superior a zero indicaria que o grupo de carga de trabalho utilizaria recursos adicionais se fosse atribuído.  Inversamente, se esta métrica for consistentemente baixa e o grupo de trabalho trabalhar *as consultas ativas* é baixa, o grupo de carga de trabalho não está a ser utilizado.  Esta situação é especialmente problemática se a *percentagem* de recursos efetivos da tampa for superior a zero, uma vez que isso indica ria de [carga de trabalho subutilizada.](#underutilized-workload-isolation)|Min, Avg, Max |
|Atribuição de grupo de carga de trabalho por cento do sistema | Esta métrica apresenta a percentagem de alocação de recursos em relação a todo o sistema.<br><br>Considere um grupo de trabalho `DataLoads` com um `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurado em 25%.  *A atribuição* do grupo de carga de trabalho por valor por cento do sistema filtrado para `DataLoads` seria de 25% (25% / 100%) se uma única consulta estivesse a decorrer neste grupo de carga de trabalho.|Min, Avg, Max |
|Intervalos de consulta de grupo de trabalho |Consultas para o grupo de trabalho que tem cronometrado.  Os intervalos de consulta reportados por esta métrica são apenas uma vez que a consulta tenha começado a ser executada (não inclui tempo de espera devido a bloqueioou ou esperas de recursos).<br><br>O tempo de consulta é configurado utilizando o parâmetro `QUERY_EXECUTION_TIMEOUT_SEC` na sintaxe [CREATE WORKLOAD GROUP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Aumentar o valor poderia reduzir o número de intervalos de consultas.<br><br>Considere aumentar o parâmetro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para o grupo de carga de trabalho para reduzir a quantidade de tempos e alocar mais recursos por consulta.  Nota, o aumento `REQUEST_MIN_RESOURCE_GRANT_PERCENT` reduz a quantidade de moeda para o grupo de carga de trabalho. |Soma |
|Consultas de grupo de trabalho em fila | Consultas para o grupo de carga de trabalho que está atualmente na fila à espera de iniciar a execução.  As consultas podem ser filas porque estão à espera de recursos ou fechaduras.<br><br>As perguntas podem estar à espera de inúmeras razões.  Se o sistema estiver sobrecarregado e a procura de moeda for maior do que a disponível, as consultas irão fazer fila.<br><br>Considere adicionar mais recursos ao grupo de carga de trabalho, aumentando o parâmetro `CAP_PERCENTAGE_RESOURCE` na declaração do [GRUPO CREATE WORKLOAD.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Se `CAP_PERCENTAGE_RESOURCE` for maior do que a métrica de percentagem de *recursos da tampa eficaz,* o isolamento configurado da carga de trabalho para outro grupo de carga de trabalho está a afetar os recursos atribuídos a este grupo de carga de trabalho.  Considere baixar `MIN_PERCENTAGE_RESOURCE` de outros grupos de carga de trabalho ou aumentar a instância para adicionar mais recursos. |Soma |

## <a name="monitoring-scenarios-and-actions"></a>Monitorização de cenários e ações
Abaixo estão uma série de configurações de gráficos para destacar o uso métrico de gestão de carga de trabalho para resolução de problemas, juntamente com ações associadas para resolver o problema.

### <a name="underutilized-workload-isolation"></a>Isolamento de carga de trabalho subutilizada
Considere o seguinte grupo de carga de trabalho e configuração de classificação onde é criado um grupo de carga de trabalho chamado `wgPriority` e *o theCEO* `membername` é mapeado para ele usando o `wcCEOPriority` classificador de carga de trabalho.  O grupo `wgPriority` carga de trabalho tem 25% de isolamento de carga de trabalho configurado para ele (`MIN_PERCENTAGE_RESOURCE` = 25).  Cada consulta apresentada pelo *TheCEO* recebe 5% dos recursos do sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
O gráfico abaixo está configurado da seguinte forma:<br>
Métrica 1: *Por cento eficaz do recurso min* (agregação avg, `blue line`)<br>
Métrica 2: Atribuição do grupo de *carga por cento* do sistema (agregação avg, `purple line`)<br>
Filtro: [Grupo de Carga de Trabalho] = `wgPriority`<br>
![](media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) de wg.png subutilizadoo O gráfico mostra que, com 25% de isolamento de carga de trabalho, apenas 10% está a ser usado em média.  Neste caso, o valor dos parâmetros `MIN_PERCENTAGE_RESOURCE` poderia ser reduzido para entre 10 ou 15 e permitir que outras cargas de trabalho no sistema consumissem os recursos.

### <a name="workload-group-bottleneck"></a>Estrangulamento do grupo de trabalho
Considere o seguinte grupo de carga de trabalho e configuração de classificação onde é criado um grupo de carga de trabalho chamado `wgDataAnalyst` e o *`membername` DataAnalyst* é mapeado para ele usando o `wcDataAnalyst` classificador de carga de trabalho.  O grupo de carga de trabalho `wgDataAnalyst` tem 6% de isolamento de carga de trabalho configurado para ele (`MIN_PERCENTAGE_RESOURCE` = 6) e um limite de recursos de 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  Cada consulta submetida pelo *DataAnalyst* recebe 3% dos recursos do sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
O gráfico abaixo está configurado da seguinte forma:<br>
Métrica 1: *Por cento eficaz do recurso da tampa* (agregação avg, `blue line`)<br>
Métrica 2: Atribuição do grupo de *carga de trabalho por por cento máximo de recursos* (agregação avg, `purple line`)<br>
Métrica 3: Consultas em fila do grupo de *trabalho* (agregação de soma, `turquoise line`)<br>
Filtro: [Grupo de Carga de Trabalho] = `wgDataAnalyst`<br>
![](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) de pescoço-engarrafado O gráfico mostra que, com um limite de 9% nos recursos, o grupo de carga de trabalho é 90%+ utilizado (da atribuição do *grupo Workload por métrica de por cento*de recursos máximos).  Há uma fila constante de consultas, como mostra a métrica de consultas do *grupo Workload.*  Neste caso, aumentar a `CAP_PERCENTAGE_RESOURCE` para um valor superior a 9% permitirá que mais consultas sejam executadas simultaneamente.  Aumentar o `CAP_PERCENTAGE_RESOURCE` pressupõe que existem recursos suficientes disponíveis e não isolados por outros grupos de carga de trabalho.  Verifique se a tampa aumentada verificando a *métrica de percentagem de recursos da tampa efetiva*.  Se for desejada mais entrada, considere também aumentar a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para um valor superior a 3.  Aumentar a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode permitir que as consultas corram mais rápido.

## <a name="next-steps"></a>Passos seguintes
[Quickstart: Configure o isolamento da carga de trabalho utilizando t-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
[CREATE WORKLOAD GROUP (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[CRIAR A CLASSIFIER DE CARGA DE TRABALHO (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[Monitorização da utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md)

