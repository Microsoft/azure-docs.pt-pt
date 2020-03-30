---
title: Use a Powershell para definir alertas em Insights de Aplicação / Microsoft Docs
description: Automatizar a configuração de Insights de Aplicação para obter e-mails sobre alterações métricas.
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: c19cb43d08b44b55c786e750e64a83e6f0c67381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669850"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Utilizar o PowerShell para definir alertas no Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode automatizar a configuração de [alertas](../../azure-monitor/app/alerts.md) em Insights de [Aplicação](../../azure-monitor/app/app-insights-overview.md).

Além disso, pode [configurar webhooks para automatizar a sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Se quiser criar recursos e alertas ao mesmo tempo, considere utilizar um modelo de Gestor de [Recursos Azure](powershell.md).

## <a name="one-time-setup"></a>Configuração única
Se ainda não usou o PowerShell com a sua subscrição Azure antes:

Instale o módulo Powershell Azure na máquina onde pretende executar os scripts.

* Instale o instalador da [plataforma Web da Microsoft (v5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
* Use-o para instalar o Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Ligar ao Azure
Inicie o Azure PowerShell e [ligue-se à sua subscrição:](/powershell/azure/overview)

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>Receba alertas
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
Envie-me um e-mail se a resposta do servidor aos pedidos http, em média mais de 5 minutos, for mais lenta que 1 segundo. O meu recurso Application Insights chama-se IceCreamWebApp, e está no grupo de recursos Fabrikam. Sou o dono da assinatura Azure.

O GUID é o ID de subscrição (não a chave de instrumentação da aplicação).

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
Tenho uma aplicação na qual uso [trackMetric para](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) reportar uma métrica chamada "salesPerHour". Envie um e-mail aos meus colegas se o "salesPerHour" descer abaixo dos 100, em média mais de 24 horas.

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

A mesma regra pode ser utilizada para a métrica relatada utilizando o parâmetro de [medição](../../azure-monitor/app/api-custom-events-metrics.md#properties) de outra chamada de rastreio, como trackEvent ou trackPageView.

## <a name="metric-names"></a>Nomes métricos
| Nome da métrica | Nome do ecrã | Descrição |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Exceções de browser |Contagem de exceções não apanhadas lançadas no navegador. |
| `basicExceptionServer.count` |Exceções ao servidor |Contagem de exceções não manipuladas lançadas pela app |
| `clientPerformance.clientProcess.value` |Tempo de processamento do cliente |Tempo entre receber o último byte de um documento até que o DOM esteja carregado. Os pedidos de asincronização podem ainda estar a ser processados. |
| `clientPerformance.networkConnection.value` |Tempo de ligação da rede de carga de página |Tempo que o navegador demora a ligar à rede. Pode ser 0 se em cache. |
| `clientPerformance.receiveRequest.value` |Tempo de resposta de receção |Tempo entre o pedido de envio do navegador para começar a receber resposta. |
| `clientPerformance.sendRequest.value` |Enviar tempo de pedido |Tempo tomado pelo navegador para enviar pedido. |
| `clientPerformance.total.value` |Tempo de carga da página do navegador |O tempo do pedido do utilizador até do M, folhas de estilo, scripts e imagens são carregados. |
| `performanceCounter.available_bytes.value` |Memória disponível |Memória física imediatamente disponível para um processo ou para utilização do sistema. |
| `performanceCounter.io_data_bytes_per_sec.value` |Taxa io processo |Total de bytes por segundo lido e escrito para ficheiros, rede e dispositivos. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |taxa de exceção |Exceções lançadas por segundo. |
| `performanceCounter.percentage_processor_time.value` |CpU processo |A percentagem de tempo decorrido de todos os fios de processo utilizados pelo processador para instruções de execução para o processo de aplicações. |
| `performanceCounter.percentage_processor_total.value` |Hora do processador |A percentagem de tempo que o processador passa em fios não-inactivos. |
| `performanceCounter.process_private_bytes.value` |Processo bytes privados |Memória exclusivamente atribuída aos processos da aplicação monitorizada. |
| `performanceCounter.request_execution_time.value` |ASP.NET solicitar o tempo de execução |Tempo de execução do pedido mais recente. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET pedidos na fila de execução |Duração da fila do pedido de pedido de inscrição. |
| `performanceCounter.requests_per_sec.value` |taxa de pedido de ASP.NET |Taxa de todos os pedidos ao pedido por segundo a partir de ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Falhas de dependência |Contagem de chamadas falhadas feitas pela aplicação do servidor para recursos externos. |
| `request.duration` |Tempo de resposta do servidor |Tempo entre receber um pedido http e terminar o envio da resposta. |
| `request.rate` |Taxa de pedido |Taxa de todos os pedidos para o pedido por segundo. |
| `requestFailed.count` |Pedidos falhados |Contagem de pedidos http que resultaram num código de resposta >= 400 |
| `view.count` |Vistas da página |Contagem dos pedidos do utilizador do cliente para uma página web. O tráfego sintético é filtrado. |
| {o seu nome métrico personalizado} |{O seu nome métrico} |O seu valor métrico reportado pela [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) ou no [parâmetro de medição de uma chamada de rastreio](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

As métricas são enviadas por diferentes módulos de telemetria:

| Grupo métrico | Módulo de colecionador |
| --- | --- |
| basicExceptionBrowser,<br/>clientePerformance,<br/>ver |[JavaScript do navegador](../../azure-monitor/app/javascript.md) |
| performanceContador |[Desempenho](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remotaDependênciaFailed |[Dependência](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| pedido,<br/>pedidoFailed |[Pedido do servidor](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Pode [automatizar a sua resposta a um alerta.](../../azure-monitor/platform/alerts-webhooks.md) O Azure irá ligar para um endereço web à sua escolha quando um alerta for levantado.

## <a name="see-also"></a>Consulte também
* [Script para configurar Insights de Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Criar Insights de aplicação e recursos de teste web a partir de modelos](powershell.md)
* [Automatizar acoplamento do Microsoft Azure Diagnostics para obter insights de aplicação](powershell-azure-diagnostics.md)
* [Automatizar a sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md)
