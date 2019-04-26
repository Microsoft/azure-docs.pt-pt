---
title: Preparar a migração de alertas clássicos do Azure Monitor, atualizando o logic apps e runbooks
description: Saiba como modificar o webhook, a aplicação lógica e a runbooks para preparar a migração voluntária.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346894"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Preparar as aplicações lógicas e executar livros para a migração de regras de alerta clássica

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas Clássicos no Azure Monitor estão a ser descontinuadas em Julho de 2019. A ferramenta de migração para acionar a migração voluntariamente está disponível no portal do Azure e está a implementar para os clientes que utilizam regras de alerta clássicas.

Se optar por migrar voluntariamente as regras do alerta clássicas para novas regras de alerta, existem algumas diferenças entre os dois sistemas, deve estar atento. Este artigo explica as diferenças entre os dois sistemas e como pode preparar para a alteração.

## <a name="api-changes"></a>Alterações à API

As APIs usadas para criar/gerir regras de alerta clássicas (`microsoft.insights/alertrules`) são diferentes das APIs usadas para criar/gerir alertas de métricas novo (`microsoft.insights/metricalerts`). Se criar/gerir programaticamente regras de alerta clássicas hoje em dia, atualize seus scripts de implementação para trabalhar com APIs de novo.

A tabela seguinte fornece uma referência a interfaces de programação para clássicas e novas alertas.

|         |Alertas clássicos  |Novos alertas de métricas |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Modelo Azure Resource Manager | [Para alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Para novos alertas de métricas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Alterações do payload de notificação

O formato do payload de notificação é ligeiramente diferente entre [regras de alerta clássicas](alerts-webhooks.md) e [novos alertas de métricas](alerts-metric-near-real-time.md#payload-schema). Se tiver quaisquer webhook, a aplicação lógica ou ações de runbook que está a ser acionadas por regras de alerta clássicas, terá de atualizar esses pontos finais de notificação para aceitar o formato do payload de novos alertas de métricas.

Pode utilizar a tabela seguinte para mapear os campos entre o payload do webook de regra de alerta clássica e o payload de webhook de alerta de métrica de novo.

|  |Alertas clássicos  |Novos alertas de métricas |
|---------|---------|---------|
|O alerta foi ativado ou resolvido     | status       | data.status |
|Informações contextuais sobre o alerta     | context        | data.context        |
|Timestamp em que o alerta foi ativado ou resolvido      | context.timestamp       | data.context.timestamp        |
| ID de regra de alerta | context.id | data.context.id |
| Nome da regra de alerta | context.name | data.context.name |
| Descrição da regra de alerta | context.description | data.context.description |
| Condição de regra de alerta | context.condition | data.context.condition|
| Nome da métrica | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Agregação de tempo (como a métrica é agregada ao longo da janela de avaliação)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Período de avaliação | context.condition.windowSize | data.context.condition.windowSize|
| Operador (como o valor de métrica agregado é comparado com o limiar de) | context.condition.operator | data.context.condition.operator|
| Limiar | context.condition.threshold| data.context.condition.allOf[0].threshold|
| Valor de métrica | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| ID da subscrição | context.subscriptionId | data.context.subscriptionId|
| Grupo de recursos do recurso afetado | context.resourceGroup | data.context.resourceGroup|
| Nome do recurso afetado | context.resourceName | data.context.resourceName |
| Tipo do recurso afetado | context.resourceType | data.context.resourceType |
|  ID de recurso do recurso afetado | context.resourceId | data.context.resourceId |
| Uma ligação direta para a página de resumo de recursos de portal | context.portalLink | data.context.portalLink|
| Campos de payload personalizado para webhook ou aplicação lógica | properties |data.properties |

Como pode ver, ambas as cargas são semelhantes. Secção seguinte tem detalhes sobre as aplicações lógicas de exemplo e um runbook de exemplo para analisar o payload de notificação para novos alertas.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>Utilizar uma aplicação lógica que recebe uma notificação de alerta de métrica

Se estiver a utilizar aplicações lógicas com alertas clássicos, terá de modificar a sua aplicação lógica para analisar o payload de alertas de métricas de novo.

1. Crie uma nova aplicação lógica.

2. Utilize o modelo "Do Azure Monitor – métricas alerta manipulador". Este modelo tem um **pedido HTTP** acionador com o esquema apropriado definido

    ![modelo da aplicação lógica](media/alerts-migration/logic-app-template.png "modelo de alerta de métrica")

3. Adicione uma ação para alojar a sua lógica de processamento.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Utilizar um runbook de automatização que recebe uma notificação de alerta de métrica

O exemplo a seguir fornece o código do PowerShell que pode ser utilizado no runbook que pode analisar as cargas de regras de alerta de métrica clássicas e novas regras de alerta de métrica.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

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

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
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

Veja um exemplo completo de um runbook que para uma VM, quando for acionado um alerta no [documentação de automatização do Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros através de webhooks

Maioria dos [nossos parceiros que se integram com alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/partners) já suportam alertas de métricas mais recentes através de suas integrações. Integrações conhecidas que já trabalham com novos alertas de métricas estão listadas abaixo.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se estiver a utilizar uma integração de parceiros que não esteja listada aqui, certifique-se com o fornecedor de integração que a integração funciona com novos alertas de métricas.

## <a name="next-steps"></a>Passos Seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
