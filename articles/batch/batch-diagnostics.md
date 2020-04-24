---
title: Métricas, alertas e registos de diagnóstico
description: Grave e analise eventos de registo de diagnóstico para recursos da conta Azure Batch, como piscinas e tarefas.
ms.topic: article
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 7f75a8302c8ba368138e6c8edee6c6069c5031d8
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117306"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Métricas, alertas e registos de lote para avaliação e monitorização de diagnóstico

 
Este artigo explica como monitorizar uma conta de Lote utilizando características do [Monitor Azure](../azure-monitor/overview.md). O Azure Monitor recolhe [métricas](../azure-monitor/platform/data-platform-metrics.md) e [registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) para recursos na sua conta Batch. Recolha e consuma estes dados de várias formas de monitorizar a sua conta de Lote e diagnosticar problemas. Também pode configurar [alertas métricos](../azure-monitor/platform/alerts-overview.md) para que receba notificações quando uma métrica atinge um valor especificado. 

## <a name="batch-metrics"></a>Métricas do lote

As métricas são dados de telemetria Azure (também chamados contadores de desempenho) emitidos pelos seus recursos Azure que são consumidos pelo serviço Azure Monitor. As métricas de exemplo numa conta de Lote incluem: Pool Create Events, Low-Priority Node Count e Task Complete Events. 

Consulte a [lista de métricas de lote suportado](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

As métricas são:

* Ativado por padrão em cada conta de Lote sem configuração adicional
* Gerado a cada 1 minuto
* Não persistiu automaticamente, mas tem um histórico de 30 dias. Pode persistir métricas de atividade como parte da exploração de diagnóstico.

### <a name="view-metrics"></a>Ver métricas

Consulte as métricas da sua conta Batch no portal Azure. A página **de visão geral** da conta por padrão mostra o nó chave, o núcleo e as métricas de tarefa. 

Para ver todas as métricas da conta do Lote: 

1. No portal, clique em todas as contas do Lote de > **serviços**e, em seguida, clique no nome da sua conta Batch. **All services**
2. Em **Monitorização,** clique em **Métricas**.
3. Selecione uma ou mais métricas. Se desejar, selecione métricas de recursos adicionais utilizando as **Subscrições,** **grupo de recursos,** **tipo de recurso**e desistências de **recursos.**
    * Para métricas baseadas na contagem (como "Contagem central dedicada" ou "Contagem de nódeos de baixa prioridade"), utilize a agregação "Média". Para métricas baseadas em eventos (como "Pool Resize Complete Events"), utilize a agregação "Count".

> [!WARNING]
> Não utilize a agregação "Soma", que acrescenta os valores de todos os pontos de dados recebidos durante o período do gráfico
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Para recuperar as métricas programáticamente, utilize as APIs do Monitor Azure. Por exemplo, consulte [as métricas do Monitor Retrieve Azure com .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Fiabilidade métrica do lote

As métricas destinam-se a ser utilizadas para a tendência e análise de dados. A entrega métrica não está garantida e está sujeita a entrega fora de serviço, perda de dados e/ou duplicação. Não é aconselhável utilizar eventos únicos para alertar ou acionar funções. Consulte a secção de [alertas métricos do Lote](#batch-metric-alerts) para obter mais detalhes sobre como definir limiares para alertar.

As métricas emitidas nos últimos 3 minutos podem ainda ser agregação. Durante este período de tempo, os valores métricos podem ser subnoticiados.

## <a name="batch-metric-alerts"></a>Alertas métricos do lote

Opcionalmente, configure *alertas métricos* em tempo real que disparam quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta gera uma [notificação](../monitoring-and-diagnostics/insights-alerts-portal.md) que escolhe quando o alerta é "Ativado" (quando o limiar é ultrapassado e a condição de alerta é cumprida) bem como quando é "Resolvido" (quando o limiar é ultrapassado novamente e a condição já não está cumprida). O alerta com base em pontos de dados únicos não é recomendado, uma vez que as métricas estão sujeitas a entregas fora de ordem, perda de dados e/ou duplicação. O alerta deve utilizar os limiares para ter em conta estas inconsistências.

Por exemplo, é possível configurar um alerta métrico quando a sua contagem de núcleos de baixa prioridade cai para um determinado nível, para que possa ajustar a composição das suas piscinas. Recomenda-se fixar um período de 10 ou mais minutos em que os alertas disparem se a contagem média de núcleos de baixa prioridade descer abaixo do valor limiar durante todo o período. Não é aconselhável alertar num período de 1 a 5 minutos, uma vez que as métricas podem ainda estar a agregar-se.

Para configurar um alerta métrico no portal:

1. Clique em todas as contas do Lote de > **Serviços**e, em seguida, clique no nome da sua conta Batch. **All services**
2. Sob **monitorização,** clique em **regras** > de alerta**Adicione alerta métrico**.
3. Selecione uma métrica, uma condição de alerta (como quando uma métrica excede um valor específico durante um período) e uma ou mais notificações.

Também pode configurar um alerta quase em tempo real utilizando a [API REST](https://docs.microsoft.com/rest/api/monitor/). Para mais informações, consulte a visão geral dos [Alertas](../azure-monitor/platform/alerts-overview.md). Para incluir trabalho, tarefa ou informações específicas do pool nos seus alertas, consulte as informações sobre consultas de pesquisa em [Responder a eventos com Alertas de Monitor Estoque Azure](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Batch diagnostics (Diagnósticos do Batch)

Os registos de diagnóstico contêm informações emitidas pelos recursos do Azure que descrevem o funcionamento de cada recurso. Para o Batch, pode recolher os seguintes registos:

* **Eventos** de registos de serviço emitidos pelo serviço Azure Batch durante a vida útil de um recurso de lote individual como uma piscina ou tarefa. 

* **Registos de métricas** ao nível da conta. 

As definições para permitir a recolha de registos de diagnóstico não são ativadas por defeito. Ative explicitamente as definições de diagnóstico de cada conta do Lote que pretende monitorizar.

### <a name="log-destinations"></a>Destinos de log

Um cenário comum é selecionar uma conta de Armazenamento Azure como destino de registo. Para armazenar registos no Armazenamento Azure, crie a conta antes de permitir a recolha de registos. Se associou uma conta de armazenamento à sua conta Batch, pode escolher essa conta como destino de registo. 

Outros destinos opcionais para registos de diagnóstico:

* Stream Batch diagnóstico log eventos para um Hub de [Eventos Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Os Hubs de Eventos podem ingerir milhões de eventos por segundo, que pode então transformar e armazenar usando qualquer fornecedor de análise em tempo real. 

* Envie registos de diagnóstico para [registos do Monitor Azure,](../log-analytics/log-analytics-overview.md)onde pode analisá-los ou exportá-los para análise em Power BI ou Excel.

> [!NOTE]
> Pode incorrer em custos adicionais para armazenar ou processar dados de registo de diagnóstico com os serviços Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Ativar a recolha de registos de diagnóstico do Lote

1. No portal, clique em todas as contas do Lote de > **serviços**e, em seguida, clique no nome da sua conta Batch. **All services**
2. Em **Monitorização,** clique em **registos** > de**diagnóstico Ligue os diagnósticos**.
3. Nas **definições de Diagnóstico,** introduza um nome para a definição e escolha um destino de registo (conta de armazenamento existente, Hub de Eventos ou registos do Monitor Azure). Selecione o **ServiceLog** e o **AllMetrics**.

    Quando selecionar uma conta de armazenamento, delineie opcionalmente uma política de retenção. Se não especificar alguns dias para retenção, os dados são retidos durante a vida útil da conta de armazenamento.

4. Clique em **Guardar**.

    ![Batch diagnostics (Diagnósticos do Batch)](media/batch-diagnostics/diagnostics-portal.png)

Outras opções para ativar a recolha de registos incluem: use o Monitor Azure no portal para configurar as definições de diagnóstico, utilize um modelo de Gestor de [Recursos,](../azure-monitor/platform/diagnostic-settings-template.md)ou utilize o Azure PowerShell ou o Azure CLI. ver [Recolher e consumir dados de registo dos seus recursos Azure.](../azure-monitor/platform/platform-logs-overview.md)


### <a name="access-diagnostics-logs-in-storage"></a>Aceder a registos de diagnóstico no armazenamento

Se arquivar os registos de diagnóstico do Lote numa conta de armazenamento, um recipiente de armazenamento é criado na conta de armazenamento assim que um evento relacionado ocorre. As bolhas são criadas de acordo com o seguinte padrão de nomeação:

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
Cada `PT1H.json` ficheiro blob contém eventos formados por JSON que ocorreram dentro `h=12`da hora especificada no URL blob (por exemplo, ). Durante a hora atual, os `PT1H.json` eventos são anexados ao ficheiro à medida que ocorrem. O valor`m=00`minúsculo é `00`sempre, uma vez que os eventos de registo de diagnóstico são divididos em bolhas individuais por hora. (Todos os tempos estão na UTC.)

Abaixo está um `PoolResizeCompleteEvent` exemplo de `PT1H.json` uma entrada num ficheiro de registo. Inclui informações sobre o número atual e alvo de nós dedicados e de baixa prioridade, bem como o tempo de início e fim da operação:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Para obter mais informações sobre o esquema dos registos de diagnóstico na conta de armazenamento, consulte registos de [diagnóstico do Archive Azure](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Para aceder aos registos na sua conta de armazenamento programáticamente, utilize as APIs de Armazenamento. 

### <a name="service-log-events"></a>Eventos de Registo de Serviço
Os registos de serviço de lote azure, se recolhidos, contêm eventos emitidos pelo serviço De lote Azure durante o tempo de vida de um recurso de lote individual como uma piscina ou tarefa. Cada evento emitido pelo Batch é registado no formato JSON. Por exemplo, este é o corpo de uma piscina de **amostras criar evento:**

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

O serviço Batch emite atualmente os seguintes eventos de Registo de Serviço. Esta lista pode não ser exaustiva, uma vez que podem ter sido adicionados eventos adicionais desde que este artigo foi atualizado pela última vez.

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
