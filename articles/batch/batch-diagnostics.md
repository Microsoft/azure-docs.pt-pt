---
title: Métricas, alertas e registos de diagnóstico - Lote Azure / Microsoft Docs
description: Registar e analisar eventos de registo de diagnóstico para recursos da conta do Azure Batch como conjuntos e tarefas.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 68d5976a5a79dbde88b7f80b02b39793ffc86de9
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254850"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Métricas de lote, alertas e registos para a avaliação de diagnóstico e monitorização

 
Este artigo explica como monitorizar uma conta de Lote utilizando características do [Monitor Azure](../azure-monitor/overview.md). O Azure Monitor recolhe [métricas](../azure-monitor/platform/data-platform-metrics.md) e [registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) para recursos na sua conta Batch. Recolha e consumir estes dados de diversas formas de monitorizar a sua conta do Batch e diagnosticar problemas. Também pode configurar [alertas métricos](../azure-monitor/platform/alerts-overview.md) para que receba notificações quando uma métrica atinge um valor especificado. 

## <a name="batch-metrics"></a>Métricas de batch

As métricas são dados de telemetria do Azure (também chamados de contadores de desempenho) emitidos por seus recursos do Azure que são consumidos pelo serviço do Azure Monitor. Métricas de exemplo numa conta do Batch incluem: agrupamento de eventos de criar, contagem de nós de baixa prioridade e eventos de conclusão de tarefas. 

Consulte a [lista de métricas de lote suportado](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

As métricas são:

* Ativado por predefinição em cada conta do Batch sem configuração adicional
* Gerados a cada 1 minuto
* Não persistente automaticamente, mas têm um histórico de sem interrupção de 30 dias. Pode persistir métricas de atividade como parte da exploração de diagnóstico.

### <a name="view-metrics"></a>Ver métricas

Veja as métricas para a sua conta do Batch no portal do Azure. A página **de visão geral** da conta por padrão mostra o nó chave, o núcleo e as métricas de tarefa. 

Para ver todas as métricas de conta do Batch: 

1. No portal, clique em **todos os serviços** > **contas do Lote**e, em seguida, clique no nome da sua conta Batch.
2. Em **Monitorização,** clique em **Métricas**.
3. Selecione uma ou mais das métricas. Se desejar, selecione métricas de recursos adicionais utilizando as **Subscrições,** **grupo de recursos,** **tipo de recurso**e desistências de **recursos.**
    * Para métricas baseadas na contagem (como "Contagem central dedicada" ou "Contagem de nódeos de baixa prioridade"), utilize a agregação "Média". Para métricas baseadas em eventos (como "Pool Resize Complete Events"), utilize a agregação "Count".

> [!WARNING]
> Não utilize a agregação "Soma", que acrescenta os valores de todos os pontos de dados recebidos durante o período do gráfico
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Para obter métricas programaticamente, utilize as APIs de Monitor do Azure. Por exemplo, consulte [as métricas do Monitor Retrieve Azure com .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Fiabilidade de métrica de batch

Métricas se destinam a ser utilizado para análise de tendências e dados. Entrega de métrica não é garantida e está sujeitas a entrega de fora de ordem, perda de dados e/ou duplicação. O uso de eventos únicos para funções de alerta ou acionador não é recomendado. Consulte a secção de [alertas métricos do Lote](#batch-metric-alerts) para obter mais detalhes sobre como definir limiares para alertar.

Ainda podem ser agregar as métricas emitidas nos últimos 3 minutos. Durante este período de tempo, os valores de métrica podem ser inferior à real.

## <a name="batch-metric-alerts"></a>Alertas de métricas de batch

Opcionalmente, configure *alertas métricos* em tempo real que disparam quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta gera uma [notificação](../monitoring-and-diagnostics/insights-alerts-portal.md) que escolhe quando o alerta é "Ativado" (quando o limiar é ultrapassado e a condição de alerta é cumprida) bem como quando é "Resolvido" (quando o limiar é ultrapassado novamente e a condição já não está cumprida). Alertas com base nos pontos de dados único não é recomendada, as métricas estão sujeitos a entrega de fora de ordem, perda de dados e/ou duplicação. Alertas devem tornar a utilização de limiares para em conta estas inconsistências.

Por exemplo, pode querer configurar um alerta de métrica quando sua contagem de núcleos de prioridade baixa atingir um determinado nível, para que pode ajustar a composição de seus conjuntos. Recomenda-se para definir um período de 10 ou mais minutos em que os alertas acionam se o número de núcleos de média de baixa prioridade for inferior ao valor de limiar para o período de todo. Não é recomendado para alertar relativamente a um período de 1 a 5 minutos, ainda podem ser agregar as métricas.

Para configurar um alerta de métrica no portal do:

1. Clique em **Todos os serviços** > **Contas do Batch**e, em seguida, clique no nome da sua conta do Batch.
2. Em **Monitorização,** clique em regras de **alerta** > Adicionar **alerta métrico**.
3. Selecione uma métrica, uma condição de alerta (por exemplo, quando uma métrica excede um valor específico durante um período) e uma ou mais notificações.

Também pode configurar um alerta quase em tempo real utilizando a [API REST](https://docs.microsoft.com/rest/api/monitor/). Para mais informações, consulte a visão geral dos [Alertas](../azure-monitor/platform/alerts-overview.md). Para incluir trabalho, tarefa ou informações específicas do pool nos seus alertas, consulte as informações sobre consultas de pesquisa em [Responder a eventos com Alertas de Monitor Estoque Azure](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Diagnóstico do batch

Os registos de diagnóstico contêm informações emitidas pelos recursos do Azure que descrevem a operação de cada recurso. Para o Batch, é possível recolher os seguintes registos:

* **Eventos** de registos de serviço emitidos pelo serviço Azure Batch durante a vida útil de um recurso de lote individual como uma piscina ou tarefa. 

* **Registos de métricas** ao nível da conta. 

Definições para ativar a recolha de registos de diagnóstico não estão ativadas por predefinição. Permitir explicitamente as definições de diagnóstico para cada conta do Batch que pretende monitorizar.

### <a name="log-destinations"></a>Destinos de log

Um cenário comum é selecionar uma conta de armazenamento do Azure como o destino de registo. Para armazenar registos no armazenamento do Azure, crie a conta antes de ativar a recolha de registos. Se tiver associado uma conta de armazenamento a sua conta do Batch, pode escolher essa conta como o destino de registo. 

Outras opções de destino para os registos de diagnóstico:

* Stream Batch diagnóstico log eventos para um Hub de [Eventos Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Os Hubs de eventos podem ingerir milhões de eventos por segundo, o que pode, em seguida, transformar e armazenar usando qualquer fornecedor de análises em tempo real. 

* Envie registos de diagnóstico para [registos do Monitor Azure,](../log-analytics/log-analytics-overview.md)onde pode analisá-los ou exportá-los para análise em Power BI ou Excel.

> [!NOTE]
> Pode implicar custos adicionais para armazenar ou processar os dados de registo de diagnóstico com serviços do Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Ativar a recolha de registos de diagnóstico do Batch

1. No portal, clique em **todos os serviços** > **contas do Lote**e, em seguida, clique no nome da sua conta Batch.
2. Em **Monitorização,** clique em **registos de diagnóstico** > ligar **diagnósticos**.
3. Nas **definições de Diagnóstico,** introduza um nome para a definição e escolha um destino de registo (conta de armazenamento existente, Hub de Eventos ou registos do Monitor Azure). Selecione o **ServiceLog** e o **AllMetrics**.

    Ao selecionar uma conta de armazenamento, se desejar, defina uma política de retenção. Se não especificar um número de dias de retenção, os dados são retidos durante o ciclo de vida da conta de armazenamento.

4. Clique em **Guardar**.

    ![Diagnóstico do batch](media/batch-diagnostics/diagnostics-portal.png)

Outras opções para ativar a recolha de registos incluem: use o Monitor Azure no portal para configurar as definições de diagnóstico, utilize um modelo de Gestor de [Recursos,](../azure-monitor/platform/diagnostic-settings-template.md)ou utilize o Azure PowerShell ou o Azure CLI. ver [Recolher e consumir dados de registo dos seus recursos Azure.](../azure-monitor/platform/platform-logs-overview.md)


### <a name="access-diagnostics-logs-in-storage"></a>Registos do diagnóstico de acesso no armazenamento

Se arquivar registos de diagnóstico do Batch numa conta de armazenamento, é criado um contentor de armazenamento na conta de armazenamento quando ocorre um evento relacionado. BLOBs são criados de acordo com o padrão de nomenclatura seguinte:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Exemplo:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Cada `PT1H.json` ficheiro blob contém eventos formados por JSON que ocorreram dentro da hora especificada no URL blob (por exemplo, `h=12`). Durante a hora atual, os eventos são anexados ao ficheiro `PT1H.json` à medida que ocorrem. O valor minúsculo (`m=00`) é sempre `00`, uma vez que os eventos de registo de diagnóstico são divididos em bolhas individuais por hora. (Todas as horas são em formato UTC).

Abaixo está um exemplo de uma entrada `PoolResizeCompleteEvent` num ficheiro de registo `PT1H.json`. Inclui informações sobre o número atual e alvo de nós dedicados e de baixa prioridade, bem como o tempo de início e fim da operação:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Para obter mais informações sobre o esquema dos registos de diagnóstico na conta de armazenamento, consulte registos de [diagnóstico do Archive Azure](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Para aceder programaticamente aos registos na sua conta de armazenamento, utilize as APIs de armazenamento. 

### <a name="service-log-events"></a>Eventos de registo do serviço
O Azure Batch registos do serviço, se recolhidos, conter eventos emitidos pelo serviço Azure Batch durante a duração de um recurso individual do Batch, como um conjunto ou tarefas. Cada evento emitido pelo Batch é registado no formato JSON. Por exemplo, este é o corpo de uma piscina de **amostras criar evento:**

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

O serviço Batch emite atualmente os seguintes eventos de registo do serviço. Esta lista não pode ser exaustiva, uma vez que os eventos adicionais foram adicionados, uma vez que este artigo foi atualizado pela última vez.

| **Eventos de Registo de Serviço** |
| --- |
| [Piscina criar](batch-pool-create-event.md) |
| [Piscina apaga início](batch-pool-delete-start-event.md) |
| [Piscina apaga completo](batch-pool-delete-complete-event.md) |
| [Início de redimensionado da piscina](batch-pool-resize-start-event.md) |
| [Redimensionar a piscina completa](batch-pool-resize-complete-event.md) |
| [Início da tarefa](batch-task-start-event.md) |
| [Tarefa completa](batch-task-complete-event.md) |
| [Falha de tarefa](batch-task-fail-event.md) |



## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Saiba mais sobre a monitorização das [soluções do Lote](monitoring-overview.md).
