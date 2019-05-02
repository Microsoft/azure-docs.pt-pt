---
title: Preparar a migração de alertas clássicos do Azure Monitor, atualizando o logic apps e runbooks
description: Saiba como modificar seu webhooks, logic apps e runbooks para preparar a migração voluntária.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 347c89991cbb4d28b46eafff0a783148793ad2f7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727480"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparar o logic apps e runbooks para a migração de regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas Clássicos no Azure Monitor estão a ser descontinuadas em Julho de 2019. Uma ferramenta de migração está disponível no portal do Azure para os clientes que utilizam regras de alerta clássicas e que desejam acionar a migração propriamente ditas.

Se optar por migrar voluntariamente as regras do alerta clássicas para novas regras de alerta, lembre-se de que existem algumas diferenças entre os dois sistemas. Este artigo explica essas diferenças e como pode preparar para a alteração.

## <a name="api-changes"></a>Alterações à API

As APIs que criar e gerir regras de alerta clássicas (`microsoft.insights/alertrules`) são diferentes das APIs que criam e gerenciam novos alertas de métricas (`microsoft.insights/metricalerts`). Se por meio de programação, cria e gere regras de alerta clássicas hoje, atualiza os scripts de implementação para trabalhar com as novas APIs.

A tabela seguinte é uma referência para as interfaces de programação para alertas clássicas e novas:

|         |Alertas clássicos  |Novos alertas de métricas |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Modelo Azure Resource Manager | [Para alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Para novos alertas de métricas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Alterações do payload de notificação

O formato do payload de notificação é ligeiramente diferente entre [regras de alerta clássicas](alerts-webhooks.md) e [novos alertas de métricas](alerts-metric-near-real-time.md#payload-schema). Se tiver qualquer webhook, a aplicação lógica ou a ações do runbook que são acionadas por regras de alerta clássicas, tem de atualizar esses pontos finais de notificação para aceitar o formato do payload de novos alertas de métricas.

Utilize a tabela seguinte para mapear os campos de payload de webhook do formato clássico para o novo formato:

|  |Alertas clássicos  |Novos alertas de métricas |
|---------|---------|---------|
|O alerta foi ativado ou resolvido?    | **status**       | **data.status** |
|Informações contextuais sobre o alerta     | **context**        | **data.context**        |
|Carimbo de hora em que o alerta foi ativado ou resolvido     | **context.timestamp**       | **data.context.timestamp**        |
| ID de regra de alerta | **context.id** | **data.context.id** |
| Nome da regra de alerta | **context.name** | **data.context.name** |
| Descrição da regra de alerta | **context.description** | **data.context.description** |
| Condição de regra de alerta | **context.condition** | **data.context.condition** |
| Nome da métrica | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agregação de tempo (como a métrica é agregada ao longo da janela de avaliação)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Período de avaliação | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operador (como o valor de métrica agregado é comparado com o limiar de) | **context.condition.operator** | **data.context.condition.operator** |
| Limiar | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Valor de métrica | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| ID da subscrição | **context.subscriptionId** | **data.context.subscriptionId** |
| Grupo de recursos do recurso afetado | **context.resourceGroup** | **data.context.resourceGroup** |
| Nome do recurso afetado | **context.resourceName** | **data.context.resourceName** |
| Tipo do recurso afetado | **context.resourceType** | **data.context.resourceType** |
| ID de recurso do recurso afetado | **context.resourceId** | **data.context.resourceId** |
| Ligação direta para a página de resumo de recursos de portal | **context.portalLink** | **data.context.portalLink** |
| Campos de payload personalizado para ser passada para a webhook ou aplicação lógica | **Propriedades** | **data.properties** |

As cargas são semelhantes, como pode ver. A seção a seguir oferece:

- Detalhes sobre como modificar o logic apps para trabalhar com o novo formato.
- Um exemplo de runbook que analisa o payload de notificação para novos alertas.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modificar uma aplicação lógica para receber uma notificação de alerta de métrica

Se estiver a utilizar aplicações lógicas com alertas clássicos, tem de modificar o código de aplicação lógica para analisar o payload de alertas de métricas de novo. Siga estes passos.

1. Crie uma nova aplicação lógica.

1. Utilize o modelo "Do Azure Monitor – métricas alerta manipulador". Este modelo tem um **pedido HTTP** acionador com o esquema apropriado definido.

    ![modelo da aplicação lógica](media/alerts-migration/logic-app-template.png "modelo de alerta de métrica")

1. Adicione uma ação para alojar a sua lógica de processamento.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Utilizar um runbook da automatização que recebe uma notificação de alerta de métrica

O exemplo seguinte fornece código do PowerShell para utilizar no runbook. Esse código pode analisar as cargas de regras de alerta de métrica clássicas e novas regras de alerta de métrica.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Para obter um exemplo completo de um runbook que para uma máquina virtual, quando for acionado um alerta, consulte a [documentação de automatização do Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros através de webhooks

Maioria dos [nossos parceiros que se integram com alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/partners) já suportam alertas de métricas mais recentes através de suas integrações. Integrações conhecidas que já trabalham com novos alertas de métricas são:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se estiver a utilizar uma integração de parceiros que não esteja listada aqui, certifique-se com o fornecedor de integração que a integração funciona com novos alertas de métricas.

## <a name="next-steps"></a>Passos Seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
