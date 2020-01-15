---
title: Monitoramento do portal de gerenciamento de carga de trabalho
description: Diretrizes para monitoramento do portal de gerenciamento de carga de trabalho no Azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: f3baaab59031c4cfad036a7181318502d1969715
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942428"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – monitoramento de carga de trabalho Portal de Gerenciamento (versão prévia)
Este artigo explica como monitorar a utilização de recursos do [grupo de carga de trabalho](sql-data-warehouse-workload-isolation.md#workload-groups) e a atividade de consulta. Para obter detalhes sobre como configurar o Metrics Explorer do Azure, consulte o artigo [introdução ao azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) .  Consulte a seção [utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) na documentação de monitoramento de SQL data warehouse do Azure para obter detalhes sobre como monitorar o consumo de recursos do sistema.
Há duas categorias diferentes de métricas de grupo de carga de trabalho fornecidas para monitorar o gerenciamento de carga de trabalho: alocação de recursos e atividade de consulta.  Essas métricas podem ser divididas e filtradas por grupo de carga de trabalho.  As métricas podem ser divididas e filtradas com base em se forem definidas pelo sistema (grupos de carga de trabalho da classe de recurso) ou definidos pelo usuário (criados pelo usuário com a sintaxe [Create Workload Group](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) ).

## <a name="workload-management-metric-definitions"></a>Definições de métrica de gerenciamento de carga de trabalho

|Nome da Métrica                    |Descrição  |Tipo de Agregação |
|-------------------------------|-------------|-----------------|
|Porcentagem de recursos de limite efetivo | A *porcentagem de recursos de limite efetivo* é um limite rígido na porcentagem de recursos acessíveis pelo grupo de cargas de trabalho, levando em conta a *porcentagem de recursos mín. de recurso mínima* alocada para outros grupos de carga de trabalho. A métrica de *porcentagem de recursos de Cap efetivo* é configurada usando o parâmetro `CAP_PERCENTAGE_RESOURCE` na sintaxe de [Criar grupo de carga de trabalho](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  O valor efetivo é descrito aqui.<br><br>Por exemplo, se um grupo de carga de trabalho `DataLoads` for criado com `CAP_PERCENTAGE_RESOURCE` = 100 e outro grupo de carga de trabalho for criado com um percentual de recursos mínimo efetivo de 25%, a *porcentagem de recurso Cap efetivo* para o grupo de carga de trabalho `DataLoads` será 75%.<br><br>A *porcentagem do recurso Cap efetivo* determina o limite superior de simultaneidade (e, portanto, a taxa de transferência potencial) que um grupo de carga de trabalho pode atingir.  Se uma taxa de transferência adicional for necessária além do que é relatado atualmente pela métrica de *porcentagem de recursos do Cap efetivo* , aumente o `CAP_PERCENTAGE_RESOURCE`, diminua a `MIN_PERCENTAGE_RESOURCE` de outros grupos de carga de trabalho ou dimensione a instância para adicionar mais recursos.  A diminuição da `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode aumentar a simultaneidade, mas pode não aumentar a taxa de transferência geral.| Mín., Méd. máx. |
|Percentual de recurso mínimo efetivo |*Percentual de recursos mínimos efetivos* é a porcentagem mínima de recursos reservados e isolados para o grupo de carga de trabalho levando em conta o nível de serviço mínimo.  A métrica de porcentagem de recursos mínimos efetivo é configurada usando o parâmetro `MIN_PERCENTAGE_RESOURCE` na sintaxe [Criar grupo de carga de trabalho](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  O valor efetivo é descrito [aqui](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values).<br><br>Use o tipo de agregação Sum quando essa métrica não estiver filtrada e desdividida para monitorar o isolamento de carga de trabalho total configurado no sistema.<br><br>O *percentual de recurso mínimo efetivo* determina o limite inferior da simultaneidade garantida (e, portanto, a taxa de transferência garantida) que um grupo de carga de trabalho pode atingir  Se forem necessários recursos adicionais garantidos além do que atualmente é relatado pela métrica de *porcentagem de recursos min efetivo* , aumente o `MIN_PERCENTAGE_RESOURCE` parâmetro configurado para o grupo de carga de trabalho.  A diminuição da `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode aumentar a simultaneidade, mas pode não aumentar a taxa de transferência geral. |Mín., Méd. máx.|
|Consultas ativas do grupo de cargas de trabalho  |Essa métrica relata as consultas ativas dentro do grupo de cargas de trabalho.  Usar essa métrica não filtrada e desdividir exibe todas as consultas ativas em execução no sistema.|Soma         |
|Alocação do grupo de cargas de trabalho por porcentagem máxima de recursos |Essa métrica exibe a porcentagem de alocação de recursos em relação à *porcentagem de recursos de limite efetivo* por grupo de carga de trabalho.  Essa métrica fornece a utilização efetiva do grupo de carga de trabalho.<br><br>Considere um grupo de carga de trabalho `DataLoads` com um *percentual de recurso Cap efetivo* de 75% e uma `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurada em 25%.  A *alocação do grupo de cargas de trabalho pelo valor de porcentagem de recurso máximo* filtrado para `DataLoads` seria 33% (25%/75%) se uma única consulta estava em execução neste grupo de carga de trabalho.<br><br>Use essa métrica para identificar a utilização de um grupo de carga de trabalho.  Um valor próximo a 100% indica que todos os recursos disponíveis para o grupo de carga de trabalho estão sendo usados.  Além disso, a *métrica de consultas em fila do grupo de cargas de trabalho* para o mesmo grupo de cargas de trabalho que mostra um valor maior que zero indicaria que o grupo de carga de trabalho utilizaria recursos adicionais, se alocado  Por outro lado, se essa métrica estiver consistentemente baixa e as *consultas ativas do grupo de carga de trabalho* estiverem baixas, o grupo de carga de trabalho não estará sendo utilizado.  Essa situação é especialmente problemática se o *percentual do recurso Cap efetivo* for maior que zero, pois isso indicaria um [isolamento de carga de trabalho subutilizado](#underutilized-workload-isolation).|Mín., Méd. máx. |
|Alocação do grupo de cargas de trabalho por porcentagem do sistema | Essa métrica exibe a alocação de porcentagem de recursos em relação a todo o sistema.<br><br>Considere um grupo de carga de trabalho `DataLoads` com uma `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurada em 25%.  A *alocação do grupo de carga de trabalho por valor percentual do sistema* filtrado para `DataLoads` seria 25% (25%/100%) se uma única consulta estava em execução neste grupo de carga de trabalho.|Mín., Méd. máx. |
|Tempos limite de consulta do grupo de cargas de trabalho |Consultas para o grupo de cargas de trabalho que atingiram o tempo limite.  Os tempos limite de consulta relatados por essa métrica são apenas uma vez que a consulta começou a ser executada (ela não inclui o tempo de espera devido a esperas de bloqueio ou de recursos).<br><br>O tempo limite da consulta é configurado usando o parâmetro `QUERY_EXECUTION_TIMEOUT_SEC` na sintaxe [Criar grupo de carga de trabalho](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Aumentar o valor pode reduzir o número de tempos limite de consulta.<br><br>Considere aumentar o parâmetro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para o grupo de carga de trabalho para reduzir a quantidade de tempos limite e alocar mais recursos por consulta.  Observe que o aumento `REQUEST_MIN_RESOURCE_GRANT_PERCENT` reduz a quantidade de simultaneidade para o grupo de carga de trabalho. |Soma |
|Consultas em fila do grupo de cargas de trabalho | Consultas para o grupo de cargas de trabalho que estão na fila no momento aguardando para iniciar a execução.  As consultas podem ser enfileiradas porque estão aguardando recursos ou bloqueios.<br><br>As consultas podem estar aguardando por vários motivos.  Se o sistema estiver sobrecarregado e a demanda de simultaneidade for maior que o que está disponível, as consultas serão enfileiradas.<br><br>Considere adicionar mais recursos ao grupo de cargas de trabalho aumentando o parâmetro `CAP_PERCENTAGE_RESOURCE` na instrução [Create Workload Group](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Se `CAP_PERCENTAGE_RESOURCE` for maior que a métrica de *porcentagem de recursos de limite efetivo* , o isolamento de carga de trabalho configurado para outro grupo de carga de trabalho afetará os recursos alocados para esse grupo de carga de trabalho.  Considere reduzir `MIN_PERCENTAGE_RESOURCE` de outros grupos de carga de trabalho ou escalar verticalmente a instância para adicionar mais recursos. |Soma |

## <a name="monitoring-scenarios-and-actions"></a>Cenários e ações de monitoramento
Veja abaixo uma série de configurações de gráfico para destacar o uso da métrica de gerenciamento de carga de trabalho para solução de problemas, juntamente com as ações associadas para resolver o problema.

### <a name="underutilized-workload-isolation"></a>Isolamento de carga de trabalho subutilizado
Considere o seguinte grupo de carga de trabalho e configuração de classificador em que um grupo de carga de trabalho chamado `wgPriority` é criado e *TheCEO* `membername` é mapeado para ele usando o classificador de carga de trabalho `wcCEOPriority`.  O grupo de carga de trabalho `wgPriority` tem 25% de isolamento de carga de trabalho configurado para ele (`MIN_PERCENTAGE_RESOURCE` = 25).  Cada consulta enviada por *TheCEO* recebe 5% dos recursos do sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
O gráfico abaixo é configurado da seguinte maneira: métrica 1: *percentual de recurso mínimo efetivo* (agregação média, `blue line`) métrica 2: *alocação de grupo de carga de trabalho por porcentagem do sistema* (média de agregação, `purple line`) filtro: [grupo de cargas de trabalho] = `wgPriority`
![Underutilized-WG.  Nesse caso, o valor do parâmetro `MIN_PERCENTAGE_RESOURCE` pode ser reduzido para 10 ou 15 e permitir que outras cargas de trabalho no sistema consumam os recursos.

### <a name="workload-group-bottleneck"></a>Afunilamento do grupo de carga de trabalho
Considere o seguinte grupo de cargas de trabalho e a configuração de classificador em que um grupo de carga de trabalho chamado `wgDataAnalyst` é criado e o `membername` de *Webalistas* é mapeado para ele usando o classificador de carga de trabalho `wcDataAnalyst`  O grupo de carga de trabalho `wgDataAnalyst` tem 6% de isolamento de carga de trabalho configurado para ele (`MIN_PERCENTAGE_RESOURCE` = 6) e um limite de recursos de 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  Cada consulta enviada pelo *analista* de dados recebe 3% dos recursos do sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
O gráfico abaixo é configurado da seguinte maneira: métrica 1: *porcentagem de recursos de Cap efetivo* (média de agregação, `blue line`) métrica 2: *alocação do grupo de cargas de trabalho por porcentagem máxima de recursos* (agregação média, `purple line`) métrica 3: grupo de cargas de *trabalho consultas em fila* (agregação de soma, `turquoise line`) filtro: [grupo de cargas de trabalho] = `wgDataAnalyst`
![garrafa-pescoço-WG](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) o gráfico mostra que, com um limite de 9% nos recursos, o grupo de carga de trabalho é 90% * métrica de porcentagem de recurso*).  Há um enfileiramento constante de consultas, conforme mostrado na *métrica de consultas em fila do grupo de cargas de trabalho*.  Nesse caso, aumentar o `CAP_PERCENTAGE_RESOURCE` para um valor maior que 9% permitirá que mais consultas sejam executadas simultaneamente.  Aumentar o `CAP_PERCENTAGE_RESOURCE` pressupõe que há recursos suficientes disponíveis e não são isolados por outros grupos de carga de trabalho.  Verifique a Cap aumentada verificando a *métrica percentual de recurso Cap efetivo*.  Se mais taxa de transferência for desejada, considere também aumentar o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para um valor maior que 3.  Aumentar o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode permitir que as consultas sejam executadas mais rapidamente.

## <a name="next-steps"></a>Passos seguintes
[Início rápido: configurar o isolamento de carga de trabalho usando o T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
[Criar grupo de cargas de trabalho (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[CRIAR CLASSIFICADOr de carga de trabalho (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[Monitorando a utilização de recursos](sql-data-warehouse-concept-resource-utilization-query-activity.md)

