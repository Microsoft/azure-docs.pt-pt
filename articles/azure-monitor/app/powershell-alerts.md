---
title: Utilizar o Powershell para definir alertas no Application Insights | Documentos da Microsoft
description: Automatize a configuração do Application Insights para obter e-mails sobre as alterações de métrica.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: 1da3b71cbb809c92ba3228676f8a47235829b499
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309541"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Utilizar o PowerShell para definir alertas no Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode automatizar a configuração do [alertas](../../azure-monitor/app/alerts.md) na [Application Insights](../../azure-monitor/app/app-insights-overview.md).

Além disso, pode [definir webhooks para automatizar a sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Se quiser criar alertas e recursos ao mesmo tempo, considere [com um modelo Azure Resource Manager](powershell.md).

## <a name="one-time-setup"></a>Configuração de uso individual
Se ainda não utilizou o PowerShell com a sua subscrição do Azure antes de:

Instale o módulo Azure Powershell na máquina em que pretende executar os scripts.

* Instale [Microsoft Web Platform Installer (v5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
* Utilize-o para instalar o Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Ligar ao Azure
Inicie o Azure PowerShell e [ligar à sua subscrição](/powershell/azure/overview):

```PowerShell

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
Enviar-me e-mail se a resposta do servidor para pedidos HTTP, apresentou uma média de mais de 5 minutos, é mais lenta do que 1 segundo. O meu recurso do Application Insights é chamado IceCreamWebApp e ele está no grupo de recursos Fabrikam. Sou o proprietário da subscrição do Azure.

O GUID é o ID de subscrição (não a chave de instrumentação do aplicativo).

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
Tenho um aplicativo em que uso [trackmetric ()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) para reportar uma métrica com o nome "salesPerHour." Envie que um e-mail para meus colegas se "salesPerHour" cai abaixo de 100, uma média de mais de 24 horas.

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

A mesma regra pode ser utilizada para a métrica comunicada ao utilizar o [parâmetro de medida](../../azure-monitor/app/api-custom-events-metrics.md#properties) da chamada de outro controle como TrackEvent ou trackPageView.

## <a name="metric-names"></a>Nomes de métrica
| Nome da métrica | Nome do ecrã | Descrição |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Exceções de browser |Número de exceções não identificadas emitidas no browser. |
| `basicExceptionServer.count` |Exceções de servidor |Contagem de exceções não processadas emitidas pela aplicação |
| `clientPerformance.clientProcess.value` |Tempo de processamento do cliente |Tempo entre a receção do último byte de um documento, até que o DOM ser carregado. Pedidos assíncronos podem continuar a ser processados. |
| `clientPerformance.networkConnection.value` |Tempo de ligação da rede de carregamento de páginas |Tempo que o navegador é conectado à rede. Pode ser 0, se em cache. |
| `clientPerformance.receiveRequest.value` |Tempo de resposta de receção |Tempo entre a enviar pedido para começar a receber a resposta do navegador. |
| `clientPerformance.sendRequest.value` |Tempo de resposta de envio |Tempo decorrido ao navegador para enviar o pedido. |
| `clientPerformance.total.value` |Tempo de carregamento de páginas do browser |Tempo entre o pedido do utilizador e o carregamento de DOM, de folhas de estilos, de scripts e de imagens. |
| `performanceCounter.available_bytes.value` |Memória disponível |Memória física imediatamente disponível para um processo ou para utilização do sistema. |
| `performanceCounter.io_data_bytes_per_sec.value` |Velocidade de E/S do Processo |Total de bytes por segundo lidos e escritos em ficheiros, na rede e em dispositivos. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |taxa de exceções |Exceções geradas por segundo. |
| `performanceCounter.percentage_processor_time.value` |CPU do processo |A percentagem de tempo decorrido de todos os threads de processo utilizada pelo processador para instruções de execução para o processo de aplicativos. |
| `performanceCounter.percentage_processor_total.value` |Tempo do processador |Percentagem de tempo que o processador despende em threads não inativos. |
| `performanceCounter.process_private_bytes.value` |Bytes privados do processo |Memória atribuída exclusivamente aos processos da aplicação monitorizada. |
| `performanceCounter.request_execution_time.value` |Tempo de execução de solicitação do ASP.NET |Tempo de execução do pedido mais recente. |
| `performanceCounter.requests_in_application_queue.value` |Solicitações ASP.NET na fila de execução |Tamanho da fila de pedidos da aplicação. |
| `performanceCounter.requests_per_sec.value` |Taxa de pedidos do ASP.NET |Classificação de todos os pedidos da aplicação por segundo do ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Falhas de dependência |Contagem de chamadas falhadas efetuadas pela aplicação de servidor para recursos externos. |
| `request.duration` |Tempo de resposta do servidor |Tempo entre a receção de um pedido HTTP e a conclusão do envio da resposta. |
| `request.rate` |Taxa do pedido |Taxa de todos os pedidos para a aplicação por segundo. |
| `requestFailed.count` |Pedidos falhados |Número de pedidos HTTP que resultaram num código de resposta > = 400 |
| `view.count` |Visualizações da página |Contagem de pedidos de utilizador do cliente para uma página da web. Tráfego sintético é filtrado. |
| {o nome métrico personalizado} |{O seu nome de métrica} |O valor de métrica comunicado pelo [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) ou no [parâmetro de medições de uma chamada de controlo](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

As métricas são enviadas por módulos de telemetria diferentes:

| Grupo de métrica | Módulo de recoletor |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>ver |[Browser JavaScript](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Performance](../../azure-monitor/app/configuration-with-applicationinsights-config.md) (Desempenho) |
| remoteDependencyFailed |[Dependência](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| solicitação,<br/>requestFailed |[Pedido do servidor](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Pode [automatizar a sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md). Azure chamará um endereço web da sua preferência, quando é desencadeado um alerta.

## <a name="see-also"></a>Consulte também
* [Script para configurar o Application Insights](powershell-script-create-resource.md)
* [Criar o Application Insights e recursos de teste da web a partir de modelos](powershell.md)
* [Automatizar o acoplamento Microsoft Azure Diagnostics para o Application Insights](powershell-azure-diagnostics.md)
* [Automatizar a sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md)
