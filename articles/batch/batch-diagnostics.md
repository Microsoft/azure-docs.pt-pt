---
title: Métricas, alertas e registos de diagnóstico
description: Grave e analise os eventos de registo de diagnóstico para recursos de conta Azure Batch, como piscinas e tarefas.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: 265149e8d3cd775974ec690ebffbce92a1b82b2e
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91848692"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Métricas, alertas e registos de diagnóstico

Este artigo explica como monitorizar uma conta batch utilizando características do [Azure Monitor](../azure-monitor/overview.md). O Azure Monitor recolhe [métricas](../azure-monitor/platform/data-platform-metrics.md) e [registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) para recursos na sua conta Batch. Recolher e consumir estes dados de várias formas de monitorizar a sua conta Batch e diagnosticar problemas. Também pode configurar [alertas métricos](../azure-monitor/platform/alerts-overview.md) para que receba notificações quando uma métrica atinge um valor especificado.

## <a name="batch-metrics"></a>Métricas de lote

As métricas são dados de telemetria Azure (também chamados contadores de desempenho) que são emitidos pelos seus recursos Azure e consumidos pelo serviço Azure Monitor. Exemplos de métricas numa conta de Lote são Eventos de Criação de Pool, Low-Priority Contagem de Nó e Eventos Completos de Tarefas.

Consulte a [lista de métricas do Lote suportado.](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)

As métricas são:

- Ativado por padrão em cada conta Batch sem configuração adicional
- Gerado a cada 1 minuto
- Não persistiram automaticamente, mas têm uma história de 30 dias. Pode persistir métricas de atividade como parte do registo de diagnóstico.

## <a name="view-batch-metrics"></a>Ver métricas de lote

No portal Azure, a página **'Visão Geral'** da conta apresentará por predeza as métricas do nó, núcleo e tarefa da chave.

Para ver todas as métricas da conta do Lote no portal Azure:

1. No portal Azure, selecione **todas as**contas de Lote de  >  **Serviços**e, em seguida, selecione o nome da sua conta Batch.
2. Em **Monitorização**, selecione **Métricas**.
3. **Selecione Adicione a métrica** e, em seguida, escolha uma métrica da lista de dropdown.
4. Selecione uma opção **de agregação** para a métrica. Para métricas baseadas na contagem (como "Contagem de Núcleos Dedicado" ou "Contagem de nó de baixa prioridade"), utilize a agregação **média.** Para métricas baseadas em eventos (como "Pool Resize Complete Events"), utilize a agregação **do Conde.**

   > [!WARNING]
   > Não utilize a agregação "Sum", que adiciona os valores de todos os pontos de dados recebidos durante o período do gráfico.

5. Para adicionar métricas adicionais, repita os passos 3 e 4.

Também pode obter métricas programáticas com as APIs do Monitor Azure. Por exemplo, consulte [as métricas do Monitor De Recuperação com .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

### <a name="batch-metric-reliability"></a>Fiabilidade da métrica do lote

As métricas podem ajudar a identificar tendências e podem ser usadas para análise de dados. É importante notar que a entrega métrica não está garantida, podendo estar sujeita a entrega fora de encomenda, perda de dados e/ou duplicação. Por isso, não é recomendado o uso de eventos individuais para alertar ou acionar funções. Consulte a secção seguinte para obter mais detalhes sobre como definir limiares para alerta.

As métricas emitidas nos últimos 3 minutos podem ainda ser agregadas, pelo que os valores métricos podem ser subnoticiados durante este período de tempo.

## <a name="batch-metric-alerts"></a>Alertas métricos do lote

Pode configurar perto de *alertas métricos* em tempo real que desencadeiam quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta gera uma notificação quando o alerta é "Ativado" (quando o limiar é cruzado e a condição de alerta é cumprida) bem como quando é "Resolvido" (quando o limiar é cruzado novamente e a condição já não é cumprida).

Os alertas que desencadeiam um único ponto de dados não são recomendados, uma vez que as métricas estão sujeitas a entrega fora de encomenda, perda de dados e/ou duplicação. Ao criar os seus alertas, pode utilizar limiares para responder a estas inconsistências.

Por exemplo, é melhor configurar um alerta métrico quando a sua contagem de núcleo de baixa prioridade cai para um determinado nível, para que possa ajustar a composição das suas piscinas. Para obter os melhores resultados, desaccione um período de 10 ou mais minutos, onde os alertas disparam se a contagem média de base de baixa prioridade ficar abaixo do valor limiar de todo o período. Isto permite que mais tempo para as métricas se agregarem para obter resultados mais precisos.

Para configurar um alerta métrico no portal Azure:

1. Selecione **todas as**contas do Lote de  >  **Serviços**e, em seguida, selecione o nome da sua conta Batch.
2. Em **Monitorização**, selecione **Alertas**e, em seguida, selecione **Nova regra de alerta**.
3. Clique **Em Selecionar a condição**e, em seguida, escolha uma métrica. Confirme os valores relativos ao **período de gráfico**, tipo **limiar,** **operador**e tipo **de agregação,** e introduza um **valor limiar**. Em seguida, selecione **Done** (Concluído).
4. Adicione um grupo de ação ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
5. Na secção de detalhes da **regra de alerta,** insira um **nome de regra de alerta** e **descrição** e selecione a **Severidade**
6. Selecione **Criar regra de alerta**.

Para obter mais informações sobre a criação de alertas métricos, consulte [Como os alertas métricos funcionam no Azure Monitor](../azure-monitor/platform/alerts-metric-overview.md) e [Criar, ver e gerir alertas métricos usando o Azure Monitor](../azure-monitor/platform/alerts-metric.md).

Também pode configurar um alerta quase em tempo real utilizando a [API do](/rest/api/monitor/)Monitor Azure . Para mais informações, consulte [a visão geral dos alertas no Microsoft Azure](../azure-monitor/platform/alerts-overview.md). Para incluir informações específicas de trabalho, tarefa ou piscina nos seus alertas, consulte as informações sobre consultas de pesquisa em [Responder a eventos com Alertas monitores Azure](../azure-monitor/learn/tutorial-response.md).

## <a name="batch-diagnostics"></a>Batch diagnostics (Diagnósticos do Batch)

Os registos de diagnóstico contêm informações emitidas pelos recursos da Azure que descrevem o funcionamento de cada recurso. Para o Lote, pode recolher os seguintes registos:

- **Serviço regista eventos emitidos** pelo serviço Azure Batch durante o tempo de vida de um recurso individual do Batch como uma piscina ou tarefa.
- **Registos de métricas** ao nível da conta.

As definições para permitir a recolha de registos de diagnóstico não são ativadas por predefinição. Ativar explicitamente as definições de diagnóstico de cada conta Batch que pretende monitorizar.

### <a name="log-destinations"></a>Destinos de registo

Um cenário comum é selecionar uma conta de Armazenamento Azure como destino de registo. Para armazenar registos no Azure Storage, crie a conta antes de ativar a recolha de registos. Se associar uma conta de armazenamento à sua conta Batch, pode escolher essa conta como destino de registo.

Alternadamente, pode:

- Stream Batch diagnostic registrs para um [Azure Event Hub](../event-hubs/event-hubs-about.md). Os Centros de Eventos podem ingerir milhões de eventos por segundo, que podes transformar e armazenar usando qualquer fornecedor de análise em tempo real.
- Envie registos de diagnóstico para [registos do Azure Monitor,](../azure-monitor/log-query/log-query-overview.md)onde pode analisá-los ou exportá-los para análise em Power BI ou Excel.

> [!NOTE]
> Pode incorrer em custos adicionais para armazenar ou processar dados de registo de diagnóstico com os serviços Azure.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Ativar a recolha de registos de diagnóstico batch

Para criar uma nova definição de diagnóstico no portal Azure, siga os passos abaixo.

1. No portal Azure, selecione **todas as**contas de Lote de  >  **Serviços**e, em seguida, selecione o nome da sua conta Batch.
2. Em **Monitorização**, selecione **Definições de diagnóstico**.
3. Nas **definições de Diagnóstico**, selecione Adicionar a **definição de diagnóstico**.
4. Insira um nome para a definição.
5. Selecione um destino: **Enviar para Log Analytics,** **Arquivar para uma conta de armazenamento,** ou **transmitir para um Centro de Eventos.** Se selecionar uma conta de armazenamento, pode definir opcionalmente uma política de retenção. Se não especificar um número de dias para a retenção, os dados são retidos durante a vida útil da conta de armazenamento.
6. **Selecione ServiceLog**, **AllMetrics,** ou ambos.
7. **Selecione Guardar** para criar a definição de diagnóstico.

Também pode [ativar a recolha através do Azure Monitor no portal Azure](../azure-monitor/platform/diagnostic-settings.md) para configurar definições de diagnóstico, utilizando um [modelo de Gestor de Recursos](../azure-monitor/platform/diagnostic-settings-template.md), ou com a Azure PowerShell ou o Azure CLI. Para mais informações, consulte [a visão geral dos registos da plataforma Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Aceder a registos de diagnósticos no armazenamento

Se arquivar registos de diagnóstico do Batch numa conta de armazenamento, é criado um recipiente de armazenamento na conta de armazenamento assim que ocorre um evento relacionado. As bolhas são criadas de acordo com o seguinte padrão de nomeação:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Cada `PT1H.json` ficheiro blob contém eventos formatados por JSON que ocorreram dentro da hora especificada no URL blob (por exemplo, `h=12` ). Durante a hora presente, os eventos são anexados ao ficheiro à `PT1H.json` medida que ocorrem. O valor minúsculo `m=00` é sempre , uma vez que os `00` eventos de registo de diagnóstico são divididos em bolhas individuais por hora. (Todos os tempos estão na UTC.)

Abaixo está um exemplo de uma `PoolResizeCompleteEvent` entrada num ficheiro de `PT1H.json` registo. Inclui informações sobre o número atual e alvo de nós dedicados e de baixa prioridade, bem como sobre o início e o fim da operação:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Para obter mais informações sobre o esquema dos registos de diagnóstico na conta de armazenamento, consulte [os registos de recursos do Archive Azure para a conta de armazenamento.](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) Para aceder aos registos na sua conta de armazenamento programáticamente, utilize as APIs de armazenamento.

### <a name="service-log-events"></a>Eventos de registo de serviço

Os registos de serviço Azure Batch, se recolhidos, contêm eventos emitidos pelo serviço Azure Batch durante o período de vida de um recurso individual do Batch, como uma piscina ou tarefa. Cada evento emitido pelo Batch é registado no formato JSON. Por exemplo, este é o corpo de um **evento de criação**de amostras:

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
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Os eventos de registo de serviço emitidos pelo serviço Batch incluem o seguinte:

- [Criar piscina](batch-pool-create-event.md)
- [Início de eliminação de piscinas](batch-pool-delete-start-event.md)
- [Excluir piscina completa](batch-pool-delete-complete-event.md)
- [Início de redimensionar piscina](batch-pool-resize-start-event.md)
- [Piscina redimensionar completo](batch-pool-resize-complete-event.md)
- [Autoescala da piscina](batch-pool-autoscale-event.md)
- [Início de tarefa](batch-task-start-event.md)
- [Tarefa concluída](batch-task-complete-event.md)
- [Falha de tarefa](batch-task-fail-event.md)
- [Falha no horário de tarefas](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
- Saiba mais sobre [a monitorização das soluções do Batch.](monitoring-overview.md)
