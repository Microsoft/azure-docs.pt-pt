---
title: Usar o PowerShell para definir alertas no Application Insights | Microsoft Docs
description: Automatize a configuração de Application Insights para obter emails sobre alterações de métricas.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/31/2016
ms.openlocfilehash: 0771079eb338c2f22cb0b1f31c48b0b9f80a3ff5
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819084"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Utilizar o PowerShell para definir alertas no Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você pode automatizar a configuração de [alertas](../../azure-monitor/app/alerts.md) no [Application insights](../../azure-monitor/app/app-insights-overview.md).

Além disso, você pode [definir WebHooks para automatizar sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Se você quiser criar recursos e alertas ao mesmo tempo, considere [usar um modelo de Azure Resource Manager](powershell.md).

## <a name="one-time-setup"></a>Configuração única
Se você não usou o PowerShell com sua assinatura do Azure antes de:

Instale o módulo do Azure PowerShell no computador em que você deseja executar os scripts.

* Instale o [Microsoft Web Platform Installer (V5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
* Use-o para instalar Microsoft Azure PowerShell

## <a name="connect-to-azure"></a>Ligar ao Azure
Inicie o Azure PowerShell e [Conecte-se à sua assinatura](/powershell/azure/overview):

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>Obter alertas
    Get-AzAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Adicionar alerta
    Add-AzMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>Exemplo 1
Enviar por email se a resposta do servidor para solicitações HTTP, com média de 5 minutos, for mais lenta que 1 segundo. Meu recurso de Application Insights é chamado de IceCreamWebApp e está no grupo de recursos fabrikam. Sou o proprietário da assinatura do Azure.

O GUID é a ID da assinatura (não a chave de instrumentação do aplicativo).

    Add-AzMetricAlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Exemplo 2
Tenho um aplicativo no qual uso [TrackMetric ()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) para relatar uma métrica chamada "salesPerHour". Envie um email para meus colegas se "salesPerHour" cair abaixo de 100, com média de 24 horas.

    Add-AzMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

A mesma regra pode ser usada para a métrica relatada usando o [parâmetro de medição](../../azure-monitor/app/api-custom-events-metrics.md#properties) de outra chamada de controle, como TrackEvent ou trackPageView.

## <a name="metric-names"></a>Nomes de métrica
| Nome da métrica | Nome da tela | Descrição |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Exceções de browser |Contagem de exceções não capturadas lançadas no navegador. |
| `basicExceptionServer.count` |Exceções de servidor |Contagem de exceções sem tratamento lançadas pelo aplicativo |
| `clientPerformance.clientProcess.value` |Tempo de processamento do cliente |Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas. |
| `clientPerformance.networkConnection.value` |Tempo de conexão de rede de carregamento de página |Tempo que o navegador leva para se conectar à rede. Pode ser 0 se armazenado em cache. |
| `clientPerformance.receiveRequest.value` |Tempo de resposta de recebimento |Tempo entre a solicitação de envio do navegador para começar a receber resposta. |
| `clientPerformance.sendRequest.value` |Enviar tempo de solicitação |Tempo gasto pelo navegador para enviar a solicitação. |
| `clientPerformance.total.value` |Tempo de carregamento da página do navegador |Tempo desde a solicitação do usuário até que DOM, folhas de estilo, scripts e imagens sejam carregados. |
| `performanceCounter.available_bytes.value` |Memória disponível |Memória física disponível imediatamente para um processo ou para uso do sistema. |
| `performanceCounter.io_data_bytes_per_sec.value` |Taxa de e/s de processo |Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |Taxa de exceção |Exceções geradas por segundo. |
| `performanceCounter.percentage_processor_time.value` |CPU do processo |A porcentagem de tempo decorrido de todos os threads de processo usados pelo processador para as instruções de execução para o processo dos aplicativos. |
| `performanceCounter.percentage_processor_total.value` |Tempo do processador |A porcentagem de tempo que o processador gasta em threads não ociosos. |
| `performanceCounter.process_private_bytes.value` |Processar bytes particulares |Memória atribuída exclusivamente aos processos do aplicativo monitorado. |
| `performanceCounter.request_execution_time.value` |Tempo de execução da solicitação ASP.NET |Tempo de execução da solicitação mais recente. |
| `performanceCounter.requests_in_application_queue.value` |Solicitações ASP.NET na fila de execução |Comprimento da fila de solicitações do aplicativo. |
| `performanceCounter.requests_per_sec.value` |Taxa de solicitação de ASP.NET |Taxa de todas as solicitações para o aplicativo por segundo de ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Falhas de dependência |Contagem de chamadas com falha feitas pelo aplicativo de servidor para recursos externos. |
| `request.duration` |Tempo de resposta do servidor |Tempo entre o recebimento de uma solicitação HTTP e a conclusão do envio da resposta. |
| `request.rate` |Taxa de solicitação |Taxa de todas as solicitações para o aplicativo por segundo. |
| `requestFailed.count` |Pedidos falhados |Contagem de solicitações HTTP que resultaram em um código de resposta > = 400 |
| `view.count` |Exibições de página |Contagem de solicitações de usuário do cliente para uma página da Web. O tráfego sintético é filtrado. |
| {seu nome de métrica personalizado} |{Seu nome de métrica} |O valor de métrica relatado por [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) ou no [parâmetro de medidas de uma chamada de rastreamento](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

As métricas são enviadas por diferentes módulos de telemetria:

| Grupo de métricas | Módulo do coletor |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>ver |[JavaScript do navegador](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Performance](../../azure-monitor/app/configuration-with-applicationinsights-config.md) (Desempenho) |
| remoteDependencyFailed |[Dependência](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| Quest<br/>requestFailed |[Solicitação do servidor](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Você pode [automatizar sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md). O Azure chamará um endereço Web de sua escolha quando um alerta for gerado.

## <a name="see-also"></a>Ver também
* [Script para configurar Application Insights](powershell-script-create-resource.md)
* [Criar Application Insights e recursos de teste na Web a partir de modelos](powershell.md)
* [Automatizar a Diagnóstico do Microsoft Azure de acoplamento a Application Insights](powershell-azure-diagnostics.md)
* [Automatizar a resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md)
