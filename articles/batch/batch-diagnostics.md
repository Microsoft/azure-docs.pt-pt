---
title: Métricas, alertas e registos de diagnóstico - Azure Batch | Documentos da Microsoft
description: Registar e analisar eventos de registo de diagnóstico para recursos da conta do Azure Batch como conjuntos e tarefas.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 368e1f6b9501c677eca1cf80d24a06c0a6533e86
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817359"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Métricas de lote, alertas e registos para a avaliação de diagnóstico e monitorização

 
Este artigo explica como monitorizar uma conta do Batch utilizar funcionalidades do [do Azure Monitor](../azure-monitor/overview.md). Monitor do Azure recolhe [métricas](../azure-monitor/platform/data-collection.md#metrics) e [registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) para recursos na sua conta do Batch. Recolha e consumir estes dados de diversas formas de monitorizar a sua conta do Batch e diagnosticar problemas. Também pode configurar [alertas de métricas](../azure-monitor/platform/alerts-overview.md) , para receber notificações quando uma métrica de atinge um valor especificado. 

## <a name="batch-metrics"></a>Métricas de batch

As métricas são dados de telemetria do Azure (também chamados de contadores de desempenho) emitidos por seus recursos do Azure que são consumidos pelo serviço do Azure Monitor. Métricas de exemplo numa conta do Batch incluem: Eventos, a contagem de nós de baixa prioridade e a tarefa de criação de conjunto concluir eventos. 

Consulte a [lista de métricas suportadas do Batch](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

As métricas são:

* Ativado por predefinição em cada conta do Batch sem configuração adicional
* Gerados a cada 1 minuto
* Não persistente automaticamente, mas têm um histórico de sem interrupção de 30 dias. Pode manter as métricas de atividade como parte do registo de diagnósticos.

### <a name="view-metrics"></a>Ver métricas

Veja as métricas para a sua conta do Batch no portal do Azure. O **descrição geral** página para a conta por predefinição mostra o nó principal, núcleos e métricas de tarefa. 

Para ver todas as métricas de conta do Batch: 

1. No portal, clique em **todos os serviços** > **contas do Batch**e, em seguida, clique no nome da sua conta do Batch.
2. Sob **monitorização**, clique em **métricas**.
3. Selecione uma ou mais das métricas. Se desejar, selecionar métricas adicionais recursos utilizando o **subscrições**, **grupo de recursos**, **tipo de recurso**, e **recursos** listas pendentes.

    ![Métricas de batch](media/batch-diagnostics/metrics-portal.png)

Para obter métricas programaticamente, utilize as APIs de Monitor do Azure. Por exemplo, veja [métricas de obter o Azure Monitor com o .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Fiabilidade de métrica de batch

Métricas se destinam a ser utilizado para análise de tendências e dados. Entrega de métrica não é garantida e está sujeitas a entrega de fora de ordem, perda de dados e/ou duplicação. O uso de eventos únicos para funções de alerta ou acionador não é recomendado. Consulte a [alertas de métricas do Batch](#batch-metric-alerts) secção para obter mais detalhes sobre como definir limiares para alertas.

Ainda podem ser agregar as métricas emitidas nos últimos 3 minutos. Durante este período de tempo, os valores de métrica podem ser inferior à real.

## <a name="batch-metric-alerts"></a>Alertas de métricas de batch

Opcionalmente, configure quase em tempo real *alertas de métricas* que acionar quando o valor de uma métrica especificado ultrapassar um limiar que atribuir. Gera o alerta uma [notificação](../monitoring-and-diagnostics/insights-alerts-portal.md) escolha quando o alerta é "ativado" (quando o limiar é cruzado e for cumprida a condição do alerta), bem como quando ele foi "resolvido" (quando o limiar é cruzado novamente e a condição é nenhuma já é cumprida). Alertas com base nos pontos de dados único não é recomendada, as métricas estão sujeitos a entrega de fora de ordem, perda de dados e/ou duplicação. Alertas devem tornar a utilização de limiares para em conta estas inconsistências.

Por exemplo, pode querer configurar um alerta de métrica quando sua contagem de núcleos de prioridade baixa atingir um determinado nível, para que pode ajustar a composição de seus conjuntos. Recomenda-se para definir um período de 10 ou mais minutos em que os alertas acionam se o número de núcleos de média de baixa prioridade for inferior ao valor de limiar para o período de todo. Não é recomendado para alertar relativamente a um período de 1 a 5 minutos, ainda podem ser agregar as métricas.

Para configurar um alerta de métrica no portal do:

1. Clique em **Todos os serviços** > **Contas do Batch**e, em seguida, clique no nome da sua conta do Batch.
2. Sob **monitorização**, clique em **regras de alerta** > **Adicionar alerta de métrica**.
3. Selecione uma métrica, uma condição de alerta (por exemplo, quando uma métrica excede um valor específico durante um período) e uma ou mais notificações.

Pode também configurar uma quase em tempo real alerta com o [REST API](https://docs.microsoft.com/rest/api/monitor/). Para obter mais informações, consulte [descrição geral dos alertas](../azure-monitor/platform/alerts-overview.md)

## <a name="batch-diagnostics"></a>Diagnóstico do batch

Os registos de diagnóstico contêm informações emitidas pelos recursos do Azure que descrevem a operação de cada recurso. Para o Batch, é possível recolher os seguintes registos:

* **Registos do serviço** eventos emitidos pelo serviço Azure Batch durante a duração de um recurso individual do Batch, como um conjunto ou tarefas. 

* **Métricas** registos ao nível da conta. 

Definições para ativar a recolha de registos de diagnóstico não estão ativadas por predefinição. Permitir explicitamente as definições de diagnóstico para cada conta do Batch que pretende monitorizar.

### <a name="log-destinations"></a>Destinos de log

Um cenário comum é selecionar uma conta de armazenamento do Azure como o destino de registo. Para armazenar registos no armazenamento do Azure, crie a conta antes de ativar a recolha de registos. Se tiver associado uma conta de armazenamento a sua conta do Batch, pode escolher essa conta como o destino de registo. 

Outras opções de destino para os registos de diagnóstico:

* Stream eventos de registo de diagnóstico do Batch para uma [Hub de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Os Hubs de eventos podem ingerir milhões de eventos por segundo, o que pode, em seguida, transformar e armazenar usando qualquer fornecedor de análises em tempo real. 

* Enviar registos de diagnóstico [registos do Azure Monitor](../log-analytics/log-analytics-overview.md), onde pode analisá-los ou exportá-los para análise no Power BI ou no Excel.

> [!NOTE]
> Pode implicar custos adicionais para armazenar ou processar os dados de registo de diagnóstico com serviços do Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Ativar a recolha de registos de diagnóstico do Batch

1. No portal, clique em **todos os serviços** > **contas do Batch**e, em seguida, clique no nome da sua conta do Batch.
2. Sob **monitorização**, clique em **registos de diagnóstico** > **ativar diagnósticos**.
3. Na **das definições de diagnóstico**, introduza um nome para a definição e escolha um destino de registo (conta de armazenamento existente, Hub de eventos ou registos do Azure Monitor). Selecione um ou ambos **ServiceLog** e **AllMetrics**.

    Ao selecionar uma conta de armazenamento, se desejar, defina uma política de retenção. Se não especificar um número de dias de retenção, os dados são retidos durante o ciclo de vida da conta de armazenamento.

4. Clique em **Guardar**.

    ![Diagnóstico do batch](media/batch-diagnostics/diagnostics-portal.png)

Outras opções para ativar a recolha de registos incluem: utilizar o Azure Monitor no portal para configurar as definições de diagnóstico, utilize um [modelo do Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md), ou utilizar o Azure PowerShell ou a CLI do Azure. ver [recolher e consumir dados de registo dos seus recursos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md#how-to-enable-collection-of-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Registos do diagnóstico de acesso no armazenamento

Se arquivar registos de diagnóstico do Batch numa conta de armazenamento, é criado um contentor de armazenamento na conta de armazenamento quando ocorre um evento relacionado. BLOBs são criados de acordo com o padrão de nomenclatura seguinte:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Exemplo:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Cada arquivo de blob PT1H.json contém eventos formatada em JSON que ocorreram dentro da hora especificada no URL do blob (por exemplo, h=12 = 12). Durante a hora presente, os eventos são acrescentados ao ficheiro PT1H.json à medida que ocorrem. O valor de minuto (m = 00) é sempre 00, uma vez que os eventos de registo de diagnóstico são divididos em blobs individuais por hora. (Todas as horas são em formato UTC).


Para obter mais informações sobre o esquema dos registos de diagnóstico na conta de armazenamento, consulte [registos de diagnóstico do Azure de arquivo](../azure-monitor/platform/archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Para aceder programaticamente aos registos na sua conta de armazenamento, utilize as APIs de armazenamento. 

### <a name="service-log-events"></a>Eventos de registo do serviço
O Azure Batch registos do serviço, se recolhidos, conter eventos emitidos pelo serviço Azure Batch durante a duração de um recurso individual do Batch, como um conjunto ou tarefas. Cada evento emitido pelo Batch é registado no formato JSON. Por exemplo, este é o corpo de uma amostra **eventos de criação de conjunto**:

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

| **Eventos de registo do serviço** |
| --- |
| [Criação de conjunto](batch-pool-create-event.md) |
| [Início de eliminação de conjunto](batch-pool-delete-start-event.md) |
| [Conjunto de eliminação completa](batch-pool-delete-complete-event.md) |
| [Início de redimensionamento de conjunto](batch-pool-resize-start-event.md) |
| [Conclusão de redimensionamento de conjunto](batch-pool-resize-complete-event.md) |
| [Zahájení úlohy](batch-task-start-event.md) |
| [Tarefa concluída](batch-task-complete-event.md) |
| [Falha de tarefa](batch-task-fail-event.md) |



## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Saiba mais sobre [monitorizar soluções de Batch](monitoring-overview.md).
