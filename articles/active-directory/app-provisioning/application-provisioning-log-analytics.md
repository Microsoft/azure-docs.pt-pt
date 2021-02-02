---
title: Compreenda como o Provisioning se integra com os registos do Azure Monitor no Azure Ative Directory.
description: Compreenda como o Provisioning se integra com os registos do Azure Monitor no Azure Ative Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 454d44eecd4eaa6d5c1f50edbd7edb1024ace041
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256886"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Entenda como o provisionamento se integra com os registos do Azure Monitor

O provisionamento integra-se com os registos do Azure Monitor e com o Log Analytics. Com a monitorização do Azure pode fazer coisas como criar livros de trabalho, também conhecidos como dashboards, armazenar registos de fornecimento por mais de 30 dias, e criar consultas e alertas personalizados. Este artigo discute como os registos de provisionamento se integram com os registos do Azure Monitor. Para saber mais sobre como os registos de provisionamento funcionam em geral, consulte [os registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>Habilitação de registos de provisionamento

Já deve estar familiarizado com a monitorização do Azure e com o Log Analytics. Caso contrário, salte para saber sobre eles e volte a aprender sobre registos de fornecimento de aplicações. Para saber mais sobre a monitorização do Azure, consulte [a visão geral do Azure Monitor](../../azure-monitor/overview.md). Para saber mais sobre os registos do Azure Monitor e o Log Analytics, consulte [a visão geral das consultas de registo no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

Uma vez configurado a monitorização do Azure, pode ativar registos para o provisionamento de aplicações. A opção está localizada na página **de definições de Diagnóstico.**

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Aceder às definições de diagnóstico" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Permitir registos de provisionamento de aplicações" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Se recentemente a provisionou um espaço de trabalho, pode demorar algum tempo até poder enviar registos para o mesmo. Se receber um erro de que a subscrição não esteja registada para utilizar *o microsoft.insights,* verifique depois de alguns minutos.
 
## <a name="understanding-the-data"></a>Compreender os dados
O fluxo de dados subjacente que o Provisioning envia aos espectadores de registo é quase idêntico. Os registos do Azure Monitor recebem quase o mesmo fluxo que o portal Azure UI e a AZure API. Existem apenas **algumas diferenças** nos campos de registo, tal como delineado na tabela seguinte. Para saber mais sobre estes campos, consulte [List provisioningObjectSummary](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta).

|Registos do Azure Monitor   |Azure portal UI   |Azure API |
|----------|-----------|------------|
|erroDescrição |reason |resultDescription |
|status |resultType |resultType |
|atividadeDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Livros do Azure Monitor

Os livros de trabalho do Azure Monitor fornecem uma tela flexível para análise de dados. Também prevêem a criação de ricos relatórios visuais dentro do portal Azure. Para saber mais, consulte [a visão geral dos livros de trabalho do Monitor Azure.](../../azure-monitor/platform/workbooks-overview.md)

O fornecimento de aplicações vem com um conjunto de livros pré-construídos. Pode encontrá-los na página de Livros de Trabalho. Para visualizar os dados, terá de garantir que todos os filtros (timeRange, jobID, appName) são preenchidos. Também terá de se certificar de que aprovisionou uma aplicação, caso contrário não haverá dados nos registos.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="A provisionamento de livros de aplicação" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Painel de provisionamento de aplicações" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Consultas personalizadas

Pode criar consultas personalizadas e mostrar os dados nos dashboards Azure. Para saber como, consulte [Criar e partilhar dashboards de dados do Log Analytics](../../azure-monitor/log-query/get-started-queries.md). Além disso, certifique-se de verificar a [visão geral das consultas de registo no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

Aqui estão algumas amostras para começar com o provisionamento de aplicações.

Consultar os registos de um utilizador com base no seu ID no sistema de origem:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Resumir a contagem por ErrorCode:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Resumir a contagem de eventos por dia por ação:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Faça 100 eventos e projeto propriedades-chave:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Alertas personalizados

O Azure Monitor permite-lhe configurar alertas personalizados para que possa ser notificado sobre eventos-chave relacionados com provisionamento. Por exemplo, é melhor receber um alerta sobre picos em falhas. Ou talvez picos em desativas ou eliminações. Outro exemplo de onde pode querer ser alertado é a falta de qualquer provisionamento, o que indica que algo está errado.

Para saber mais sobre alertas, consulte [Responder a eventos com alertas do Monitor Azure.](../../azure-monitor/learn/tutorial-response.md)

Alerta quando há um pico de falhas. Substitua o trabalhoID pelo trabalhoID para a sua aplicação.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Alerta quando há um pico de falhas." lightbox="media/application-provisioning-log-analytics/alert1.png":::

Pode haver um problema que fez com que o serviço de fornecimento deixasse de funcionar. Utilize o seguinte alerta para detetar quando não há eventos de provisionamento durante um determinado intervalo de tempo.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Pode haver um problema que fez com que o serviço de fornecimento deixasse de funcionar." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Alerta quando há um pico em desativações ou exclusões.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Alerta quando há um pico em desativações ou exclusões." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Contribuições da comunidade

Estamos a tomar uma abordagem de código aberto e baseado na comunidade para aplicações que fornecem consultas e dashboards. Se construiu uma consulta, alerta ou livro que acha que outros achariam útil, certifique-se de publicá-lo para o [AzureMonitorCommunity GitHub repo](https://github.com/microsoft/AzureMonitorCommunity). Então atire em nós um e-mail com um link. Vamos revê-lo e publicá-lo ao serviço para que outros possam beneficiar também. Pode contactar-nos em provisioningfeedback@microsoft.com .

## <a name="next-steps"></a>Passos seguintes

- [Análise de registo](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Começar com consultas em registos do Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)
- [Criar e gerir grupos de alerta no portal Azure](../../azure-monitor/platform/action-groups.md)
- [Instale e utilize as vistas de análise de registo para o Azure Ative Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Provisionamento de registos API](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta.md)