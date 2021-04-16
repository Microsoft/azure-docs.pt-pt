---
title: Métricas, alertas e registos de diagnóstico
description: Grave e analise os eventos de registo de diagnóstico para recursos de conta Azure Batch, como piscinas e tarefas.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 61aaca84b609aaf7513c6de6f0f7e73aef5a5efe
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389320"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Métricas, alertas e registos de diagnóstico

O Azure Monitor recolhe [métricas](../azure-monitor/essentials/data-platform-metrics.md) e [registos de diagnóstico](../azure-monitor/essentials/platform-logs-overview.md) para recursos na sua conta Azure Batch.

Pode recolher e consumir estes dados de várias formas de monitorizar a sua conta Batch e diagnosticar problemas. Também pode configurar [alertas métricos](../azure-monitor/alerts/alerts-overview.md) para que receba notificações quando uma métrica atinge um valor especificado.

## <a name="batch-metrics"></a>Métricas de lote

[As métricas](../azure-monitor/essentials/data-platform-metrics.md)  são dados de telemetria Azure (também chamados contadores de desempenho) que são emitidos pelos seus recursos Azure e consumidos pelo serviço Azure Monitor. Exemplos de métricas numa conta de Lote são Eventos de Criação de Pool, Low-Priority Contagem de Nó e Eventos Completos de Tarefas. Estas métricas podem ajudar a identificar tendências e podem ser usadas para análise de dados.

Consulte a [lista de métricas do Lote suportado.](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts)

As métricas são:

- Ativado por padrão em cada conta Batch sem configuração adicional
- Gerado a cada 1 minuto
- Não persistiram automaticamente, mas têm uma história de 30 dias. Pode persistir métricas de atividade como parte do registo de diagnóstico.

## <a name="view-batch-metrics"></a>Ver métricas de lote

No portal Azure, a página **'Visão Geral'** da conta Lote apresentará por predeza as métricas do nó, núcleo e tarefa da chave.

Para visualizar métricas adicionais para uma conta batch:

1. No portal Azure, selecione **todas as** contas de Lote de  >  **Serviços** e, em seguida, selecione o nome da sua conta Batch.
1. Em **Monitorização**, selecione **Métricas**.
1. **Selecione Adicione a métrica** e, em seguida, escolha uma métrica da lista de dropdown.
1. Selecione uma opção **de agregação** para a métrica. Para métricas baseadas na contagem (como "Contagem de Núcleos Dedicado" ou "Contagem de nó de baixa prioridade"), utilize a agregação **avg.** Para métricas baseadas em eventos (como "Pool Resize Complete Events"), utilize a agregação **do Conde.** Evite utilizar a agregação **Sum,** que adiciona os valores de todos os pontos de dados recebidos durante o período da tabela.
1. Para adicionar métricas adicionais, repita os passos 3 e 4.

Também pode obter métricas programáticas com as APIs do Monitor Azure. Por exemplo, consulte [as métricas do Monitor De Recuperação com .NET](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/).

> [!NOTE]
> As métricas emitidas nos últimos 3 minutos podem ainda ser agregadas, pelo que os valores podem ser sub-relatados durante este período de tempo. A entrega métrica não é garantida e pode ser afetada pela entrega fora de encomenda, perda de dados ou duplicação.

## <a name="batch-metric-alerts"></a>Alertas métricos do lote

Pode configurar perto de alertas métricos em tempo real que desencadeiam quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta gera uma notificação quando o alerta é "Ativado" (quando o limiar é cruzado e a condição de alerta é cumprida) bem como quando é "Resolvido" (quando o limiar é cruzado novamente e a condição já não é cumprida).

Como a entrega métrica pode estar sujeita a inconsistências como entrega fora de encomenda, perda de dados ou duplicação, recomendamos evitar alertas que desencadeiem num único ponto de dados. Em vez disso, utilize limiares para responder a quaisquer inconsistências, tais como entrega fora de encomenda, perda de dados e duplicação durante um período de tempo.

Por exemplo, é melhor configurar um alerta métrico quando a sua contagem de núcleo de baixa prioridade cai para um determinado nível, para que possa ajustar a composição das suas piscinas. Para obter os melhores resultados, desaccione um período de 10 ou mais minutos, onde o alerta será acionado se a contagem média de base de baixa prioridade ficar abaixo do valor limiar para todo o período. Isto permite que as métricas se agregam para obter resultados mais precisos.

Para configurar um alerta métrico no portal Azure:

1. Selecione **todas as** contas do Lote de  >  **Serviços** e, em seguida, selecione o nome da sua conta Batch.
1. Em **Monitorização**, selecione **Alertas** e, em seguida, selecione **Nova regra de alerta**.
1. **Selecione Adicionar a condição** e, em seguida, escolha uma métrica.
1. Selecione os valores pretendidos para **o período de gráfico,** **limiar,** **operador** e tipo **de agregação**.
1. Introduza um **valor limiar** e selecione a **Unidade** para o limiar.  Em seguida, selecione **Done** (Concluído).
1. Adicione um [grupo de ação](../azure-monitor/alerts/action-groups.md) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
1. Na secção de detalhes da **regra alerta,** insira um **nome de regra de alerta** e **descrição**. Se desejar que o alerta seja ativado imediatamente, certifique-se de que a **regra de alerta Desematado após a caixa de criação** é verificada.
1. Selecione **Criar regra de alerta**.

Para obter mais informações sobre a criação de alertas métricos, consulte [Como os alertas métricos funcionam no Azure Monitor](../azure-monitor/alerts/alerts-metric-overview.md) e [Criar, ver e gerir alertas métricos usando o Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

Também pode configurar um alerta quase em tempo real utilizando a [API do Monitor Azure](/rest/api/monitor/). Para mais informações, consulte [a visão geral dos alertas no Microsoft Azure](../azure-monitor/alerts/alerts-overview.md). Para incluir informações específicas de trabalho, tarefa ou piscina nos seus alertas, consulte [Responder a eventos com alertas de monitores Azure](../azure-monitor/alerts/tutorial-response.md).

## <a name="batch-diagnostics"></a>Batch diagnostics (Diagnósticos do Batch)

[Os registos de diagnóstico](../azure-monitor/essentials/platform-logs-overview.md) contêm informações emitidas pelos recursos da Azure que descrevem o funcionamento de cada recurso. Para o Lote, pode recolher os seguintes registos:

- **ServiceLog**: [eventos emitidos pelo serviço Batch](#service-log-events) durante o tempo de vida de um recurso individual, como uma piscina ou tarefa.
- **AllMetrics**: Métricas ao nível da conta Batch.

Tem de ativar explicitamente as definições de diagnóstico de cada conta Batch que pretende monitorizar.

### <a name="log-destination-options"></a>Opções de destino de registo

Um cenário comum é selecionar uma conta de Armazenamento Azure como destino de registo. Para armazenar registos no Azure Storage, crie a conta antes de ativar a recolha de registos. Se associar uma conta de armazenamento à sua conta Batch, pode escolher essa conta como destino de registo.

Alternadamente, pode:

- Stream Batch diagnostic registrs para um [Azure Event Hub](../event-hubs/event-hubs-about.md). Os Centros de Eventos podem ingerir milhões de eventos por segundo, que podes transformar e armazenar usando qualquer fornecedor de análise em tempo real.
- Envie registos de diagnóstico para [registos do Azure Monitor,](../azure-monitor/logs/log-query-overview.md)onde pode analisá-los ou exportá-los para análise em Power BI ou Excel.

> [!NOTE]
> Pode incorrer em custos adicionais para armazenar ou processar dados de registo de diagnóstico com os serviços Azure.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Ativar a recolha de registos de diagnóstico batch

Para criar uma nova definição de diagnóstico no portal Azure, siga os passos abaixo.

1. No portal Azure, selecione **todas as** contas de Lote de  >  **Serviços** e, em seguida, selecione o nome da sua conta Batch.
2. Em **Monitorização**, selecione **Definições de diagnóstico**.
3. Nas **definições de Diagnóstico**, selecione Adicionar a **definição de diagnóstico**.
4. Insira um nome para a definição.
5. Selecione um destino: **Enviar para Log Analytics,** **Arquivar para uma conta de armazenamento,** ou **transmitir para um centro de eventos.** Se selecionar uma conta de armazenamento, pode selecionar opcionalmente o número de dias para reter dados para cada registo. Se não especificar um número de dias para a retenção, os dados são retidos durante a vida útil da conta de armazenamento.
6. **Selecione ServiceLog**, **AllMetrics,** ou ambos.
7. **Selecione Guardar** para criar a definição de diagnóstico.

Também pode ativar a recolha de [registos criando definições de diagnóstico no portal Azure](../azure-monitor/essentials/diagnostic-settings.md), utilizando um [modelo de Gestor de Recursos,](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)ou utilizando o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [a visão geral dos registos da plataforma Azure](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Aceder a registos de diagnósticos no armazenamento

Se [arquivar registos de diagnóstico do Batch numa conta de armazenamento,](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)é criado um recipiente de armazenamento na conta de armazenamento assim que ocorre um evento relacionado. As bolhas são criadas de acordo com o seguinte padrão de nomeação:

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

Para aceder aos registos na sua conta de armazenamento programáticamente, utilize as APIs de armazenamento.

### <a name="service-log-events"></a>Eventos de registo de serviço

Os registos de serviço do Azure Batch contêm eventos emitidos pelo serviço Batch durante o período de vida de um recurso individual do Batch, como uma piscina ou tarefa. Cada evento emitido pelo Batch é registado no formato JSON. Por exemplo, este é o corpo de um **evento de criação** de amostras:

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
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
