---
title: Prepare-se para o Azure Monitor alerta sionantes migração atualizando as suas aplicações lógicas e livros de corridas
author: yanivlavi
description: Aprenda a modificar os seus webhooks, aplicações lógicas e livros de corridas para se preparar para a migração voluntária.
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 58ba95ff60ddccf909578a673110c870caf57376
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705569"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Prepare as suas aplicações lógicas e livros de corridas para a migração de regras clássicas de alerta

Como [já foi anunciado,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão a ser retirados em setembro de 2019 (foi originalmente julho de 2019). Uma ferramenta de migração está disponível no portal do Azure aos clientes que usam regras de alerta clássicas e que desejam disparar a migração por conta própria.

> [!NOTE]
> Devido ao atraso no lançamento da ferramenta de migração, a data de reforma para alertas clássicos migração foi alargada para 31 de agosto de 2019 a partir da data inicialmente anunciada de 30 de junho de 2019.

Se optar por migrar voluntariamente as suas regras clássicas de alerta para novas regras de alerta, esteja ciente de que existem algumas diferenças entre os dois sistemas. Este artigo explica essas diferenças e como se pode preparar para a mudança.

## <a name="api-changes"></a>Alterações de API

As APIs que criam e gerem as regras clássicas de alerta (`microsoft.insights/alertrules`) são diferentes das APIs que criam e gerem novos alertas métricos (`microsoft.insights/metricalerts`). Se criar e gerir programaticamente as regras clássicas de alerta hoje, atualize os seus scripts de implementação para trabalhar com as novas APIs.

A tabela a seguir é uma referência às interfaces programáticas para alertas clássicos e novos:

|         |Alertas clássicos  |Novos alertas métricos |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [alerta de métricas az monitor](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Modelo Azure Resource Manager | [Para alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Para novos alertas métricos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Alterações na carga útil da notificação

O formato de carga útil da notificação é ligeiramente diferente entre [as regras clássicas](alerts-webhooks.md) de alerta e [os novos alertas métricos.](alerts-metric-near-real-time.md#payload-schema) Se tiver alguma aplicação webhook, lógica ou executor asações que são desencadeadas por regras clássicas de alerta, deve atualizar esses pontos finais de notificação para aceitar o formato de carga útil de novos alertas métricos.

Utilize a tabela seguinte para mapear os campos de carga útil do webhook do formato clássico para o novo formato:

|  |Alertas clássicos  |Novos alertas métricos |
|---------|---------|---------|
|O alerta foi ativado ou resolvido?    | **status**       | **data.status** |
|Informação contextual sobre o alerta     | **contexto**        | **data.context**        |
|Carimbo de tempo em que o alerta foi ativado ou resolvido     | **context.timestamp**       | **data.context.timestamp**        |
| Id da regra de alerta | **context.id** | **data.context.id** |
| Nome da regra de alerta | **context.name** | **data.context.name** |
| Descrição da regra do alerta | **context.description** | **data.context.description** |
| Condição da regra do alerta | **context.condition** | **data.context.condition** |
| Nome da métrica | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agregação do tempo (como a métrica é agregada sobre a janela de avaliação)| **contexto.condição.timeAggregação** | **contexto.condição.timeAggregação** |
| Período de avaliação | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operador (como o valor métrico agregado é comparado com o limiar) | **context.condition.operator** | **data.context.condition.operator** |
| Limiar | **context.condition.threshold** | **data.context.condition.allOf[0].limiar** |
| Valor métrico | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| ID de Subscrição | **context.subscriptionId** | **data.context.subscriptionId** |
| Grupo de recursos do recurso afetado | **context.resourceGroup** | **data.context.resourceGroup** |
| Nome do recurso afetado | **context.resourceName** | **data.context.resourceName** |
| Tipo do recurso afetado | **context.resourceType** | **data.context.resourceType** |
| Identificação de recursos do recurso afetado | **context.resourceId** | **data.context.resourceId** |
| Link direto para a página de resumo do recurso do portal | **context.portalLink** | **data.context.portalLink** |
| Campos de carga personalizada saem para o webhook ou aplicação lógica | **Propriedades** | **data.properties** |

As cargas são semelhantes, como pode ver. A seguinte secção oferece:

- Detalhes sobre a modificação de aplicações lógicas para trabalhar com o novo formato.
- Um exemplo de livro que analisa a carga útil da notificação para novos alertas.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modificar uma aplicação lógica para receber uma notificação de alerta métrico

Se estiver a usar aplicações lógicas com alertas clássicos, tem de modificar o seu código de aplicação lógica para analisar a carga útil dos novos alertas métricos. Siga estes passos.

1. Crie uma nova aplicação lógica.

1. Utilize o modelo "Monitor Azure - Manipulador de Alerta de Métricas". Este modelo tem um gatilho **de pedido HTTP** com o esquema apropriado definido.

    ![lógica-app-modelo](media/alerts-migration/logic-app-template.png "Modelo de alerta métrico")

1. Adicione uma ação para acolher a sua lógica de processamento.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Use um livro de automação que recebe uma notificação de alerta métrico

O exemplo que se segue fornece código PowerShell para usar no seu livro de execução. Este código pode analisar as cargas para as regras clássicas de alerta métrico e novas regras de alerta métrico.

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

Para um exemplo completo de um livro de rumir que para uma máquina virtual quando um alerta é acionado, consulte a [documentação da Automação Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros através de webhooks

A maioria dos [nossos parceiros que se integram com alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/partners) já suportam novos alertas métricos através das suas integrações. Integrações conhecidas que já funcionam com novos alertas métricos são:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se está a usar uma integração de parceiros que não está listada aqui, confirme com o provedor de integração que a integração funciona com novos alertas métricos.

## <a name="next-steps"></a>Passos seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
