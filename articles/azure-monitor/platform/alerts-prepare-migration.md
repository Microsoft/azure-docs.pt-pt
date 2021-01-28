---
title: Atualizar aplicativos lógicos & runbooks para alertas migração
description: Aprenda a modificar os seus webhooks, aplicações lógicas e runbooks para se preparar para a migração voluntária.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 60a721af9acf980e88ad60504e75d2488c8a4d81
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944473"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparar as suas aplicações lógicas e runbooks para a migração de regras de alerta clássicas

> [!NOTE]
> Como [já foi anunciado,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão reformados, embora ainda em uso limitado para recursos que ainda não suportam os novos alertas. A data de reforma desses alertas foi alargada. Uma nova data será anunciada em breve.
>

Se optar por migrar voluntariamente as suas regras clássicas de alerta para novas regras de alerta, esteja ciente de que existem algumas diferenças entre os dois sistemas. Este artigo explica essas diferenças e como pode preparar-se para a mudança.

## <a name="api-changes"></a>Alterações na API

As APIs que criam e gerem regras clássicas de alerta `microsoft.insights/alertrules` são diferentes das APIs que criam e gerem novos alertas métricos `microsoft.insights/metricalerts` (). Se criar e gerir as regras clássicas de alerta hoje, atualize os scripts de implementação para trabalhar com as novas APIs.

A tabela a seguir é uma referência às interfaces programáticas tanto para alertas clássicos como para novos:

| Tipo de script de implementação | Alertas clássicos | Novos alertas métricos |
| ---------------------- | -------------- | ----------------- |
|API REST     | [microsoft.insights/alertars](/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [alerta de monitor az](/cli/azure/monitor/alert)        | [alerta de métricas de monitor az](/cli/azure/monitor/metrics/alert)        |
|PowerShell      | [Referência](/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referência](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Modelo Azure Resource Manager | [Para alertas clássicos](./alerts-enable-template.md)|[Para novos alertas métricos](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>Alterações na carga útil da notificação

O formato de carga útil de notificação é ligeiramente diferente entre [as regras clássicas de alerta](alerts-webhooks.md) e os novos [alertas métricos](alerts-metric-near-real-time.md#payload-schema). Se tiver alguma webhook, aplicação lógica ou ações de runbook que sejam desencadeadas por regras clássicas de alerta, deve atualizar esses pontos finais de notificação para aceitar o formato de carga útil de novos alertas métricos.

Utilize a seguinte tabela para mapear os campos de carga útil webhook do formato clássico para o novo formato:

| Tipo de ponto final de notificação | Alertas clássicos | Novos alertas métricos |
| -------------------------- | -------------- | ----------------- |
|O alerta foi ativado ou resolvido?    | **estado**       | **data.status** |
|Informação contextual sobre o alerta     | **contexto**        | **data.context**        |
|Carimbo de tempo em que o alerta foi ativado ou resolvido     | **contexto.timetamp**       | **data.context.timestamp**        |
| ID da regra de alerta | **context.id** | **data.context.id** |
| Nome da regra de alerta | **context.name** | **data.context.name** |
| Descrição da regra de alerta | **contexto.descrição** | **data.context.description** |
| Condição da regra de alerta | **contexto.condição** | **data.context.condition** |
| Nome da métrica | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agregação de tempo (como a métrica é agregada sobre a janela de avaliação)| **contexto.condição.timeAggregation** | **contexto.condição.timeAggregation** |
| Período de avaliação | **contexto.condition.windowSize** | **data.context.condition.windowSize** |
| Operador (como o valor métrico agregado é comparado com o limiar) | **contexto.condição.operador** | **data.context.condition.operator** |
| Limite | **contexto.condição.limiar** | **data.context.condition.allOf[0].threshold** |
| Valor métrico | **contexto.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| ID da subscrição | **context.subscriptionId** | **data.context.subscriptionId** |
| Grupo de recursos do recurso afetado | **context.resourceGroup** | **data.context.resourceGroup** |
| Nome do recurso afetado | **context.resourceName** | **data.context.resourceName** |
| Tipo do recurso afetado | **context.resourceType** | **data.context.resourceType** |
| ID de recursos do recurso afetado | **context.resourceId** | **data.context.resourceId** |
| Ligação direta à página de resumo do recurso do portal | **context.portalLink** | **data.context.portalLink** |
| Campos de carga personalizada para serem passados para o webhook ou app lógica | **propriedades** | **data.properties** |

As cargas são semelhantes, como pode ver. A seguinte secção oferece:

- Detalhes sobre a modificação de aplicações lógicas para trabalhar com o novo formato.
- Um exemplo de runbook que analisa a carga útil de notificação para novos alertas.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modifique uma aplicação lógica para receber uma notificação de alerta métrico

Se estiver a utilizar aplicações lógicas com alertas clássicos, tem de modificar o seu código de aplicação lógica para analisar a carga útil dos novos alertas métricos. Siga estes passos.

1. Crie uma nova aplicação lógica.

1. Utilize o modelo "Monitor Azure - Manipulador de Alerta de Métricas". Este modelo tem um gatilho **de pedido HTTP** com o esquema apropriado definido.

    ![A screenshot mostra dois botões, Blank Logic App e Azure Monitor – Metrics Alert Handler.](media/alerts-migration/logic-app-template.png "Modelo de alerta métrico")

1. Adicione uma ação para hospedar a sua lógica de processamento.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Use um runbook de automatização que receba uma notificação de alerta métrico

O exemplo a seguir fornece o código PowerShell para utilizar no seu runbook. Este código pode analisar as cargas para as regras clássicas de alerta métrico e novas regras de alerta métrico.

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

Para um exemplo completo de um livro de execução que para uma máquina virtual quando um alerta é acionado, consulte a documentação da [Azure Automation](../../automation/automation-create-alert-triggered-runbook.md).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros via webhooks

A maioria dos [nossos parceiros que se integram com alertas clássicos](./partners.md) já suportam alertas métricos mais recentes através das suas integrações. As integrações conhecidas que já funcionam com novos alertas métricos são:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se estiver a utilizar uma integração de parceiros que não está listada aqui, confirme com o fornecedor de integração que a integração funciona com novos alertas métricos.

## <a name="next-steps"></a>Próximos passos

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Compreender como funciona a ferramenta de migração](alerts-understand-migration.md)
