---
title: Use a Powershell para definir alertas em Insights de Aplicação Microsoft Docs
description: Automatizar a configuração do Application Insights para obter e-mails sobre alterações métricas.
ms.topic: conceptual
ms.date: 07/23/2016
ms.openlocfilehash: 74d477b6660c0f7ec2ee32b34169bb85886936e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87322470"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Utilizar o PowerShell para definir alertas no Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode automatizar a configuração de [alertas](../platform/alerts-log.md) em [Application Insights](./app-insights-overview.md).

Além disso, pode [configurar webhooks para automatizar a sua resposta a um alerta](../platform/alerts-webhooks.md).

> [!NOTE]
> Se pretender criar recursos e alertas ao mesmo tempo, considere [utilizar um modelo de Gestor de Recursos Azure](powershell.md).

## <a name="one-time-setup"></a>Configuração única
Se ainda não utilizou o PowerShell com a sua subscrição Azure:

Instale o módulo Azure Powershell na máquina onde pretende executar os scripts.

* Instale o Instalador da Plataforma Web do [Microsoft (v5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
* Use-o para instalar o Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Ligar ao Azure
Inicie a Azure PowerShell e [ligue-se à sua subscrição:](/powershell/azure/)

```azurepowershell
Add-AzAccount
```


## <a name="get-alerts"></a>Receba alertas

```azurepowershell
Get-AzAlertRule -ResourceGroup "Fabrikam" `
  [-Name "My rule"] `
  [-DetailedOutput]
```

## <a name="add-alert"></a>Adicionar alerta

```azurepowershell
Add-AzMetricAlertRule -Name "{ALERT NAME}" `
  -Description "{TEXT}" `
  -ResourceGroup "{GROUP NAME}" `
  -TargetResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
  -MetricName "{METRIC NAME}" `
  -Operator GreaterThan `
  -Threshold {NUMBER}  `
  -WindowSize {HH:MM:SS} `
  [-SendEmailToServiceOwners] `
  [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM"] `
  -Location "East US" // must be East US at present `
  -RuleType Metric
```


## <a name="example-1"></a>Exemplo 1
Envie-me um e-mail se a resposta do servidor aos pedidos HTTP, média superior a 5 minutos, for mais lenta do que 1 segundo. O meu recurso Application Insights chama-se IceCreamWebApp e está no grupo de recursos Fabrikam. Sou o dono da assinatura do Azure.

O GUID é o ID de subscrição (não a chave de instrumentação da aplicação).

```azurepowershell
Add-AzMetricAlertRule -Name "slow responses" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "request.duration" `
  -Operator GreaterThan `
  -Threshold 1 `
  -WindowSize 00:05:00 `
  -SendEmailToServiceOwners `
  -Location "East US" `
  -RuleType Metric
```

## <a name="example-2"></a>Exemplo 2
Tenho uma aplicação na qual uso [trackMetric](./api-custom-events-metrics.md#trackmetric) para reportar uma métrica chamada "salesPerHour". Envie um e-mail aos meus colegas se o "salesPerHour" cair abaixo dos 100, em média, mais de 24 horas.

```azurepowershell
Add-AzMetricAlertRule -Name "poor sales" `
  -Description "slow sales alert" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "salesPerHour" `
  -Operator LessThan `
  -Threshold 100 `
  -WindowSize 24:00:00 `
  -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
  -Location "East US" `
  -RuleType Metric
```

A mesma regra pode ser usada para a métrica relatada utilizando o parâmetro de [medição](./api-custom-events-metrics.md#properties) de outra chamada de rastreio, como TrackEvent ou trackPageView.

## <a name="metric-names"></a>Nomes das métricas
| Nome da métrica | Nome do ecrã | Descrição |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Exceções de browser |Contagem de exceções não realizadas no navegador. |
| `basicExceptionServer.count` |Exceções do servidor |Contagem de exceções não tratadas lançadas pela app |
| `clientPerformance.clientProcess.value` |Tempo de processamento do cliente |Tempo entre receber o último byte de um documento até que o DOM seja carregado. Os pedidos de async ainda podem estar a ser processados. |
| `clientPerformance.networkConnection.value` |Tempo de ligação da rede de carga de página |Tempo que o navegador demora a ligar à rede. Pode ser 0 se em cache. |
| `clientPerformance.receiveRequest.value` |Tempo de resposta de receção |Tempo entre o pedido de envio do navegador para começar a receber resposta. |
| `clientPerformance.sendRequest.value` |Enviar tempo de pedido |Tempo tomado pelo navegador para enviar pedido. |
| `clientPerformance.total.value` |Tempo de carregamento da página do navegador |Tempo do pedido do utilizador até dom, folhas de estilo, scripts e imagens são carregados. |
| `performanceCounter.available_bytes.value` |Memória disponível |Memória física imediatamente disponível para um processo ou para uso do sistema. |
| `performanceCounter.io_data_bytes_per_sec.value` |Taxa io de processo |Total de bytes por segundo lido e escrito para ficheiros, rede e dispositivos. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |taxa de exceção |Exceções lançadas por segundo. |
| `performanceCounter.percentage_processor_time.value` |Processo CPU |A percentagem de tempo decorrido de todos os fios de processo utilizados pelo processador para instruções de execução para o processo de aplicações. |
| `performanceCounter.percentage_processor_total.value` |Tempo do processador |A percentagem de tempo que o processador gasta em fios não-idle. |
| `performanceCounter.process_private_bytes.value` |Processar bytes privados |Memória exclusivamente atribuída aos processos da aplicação monitorizada. |
| `performanceCounter.request_execution_time.value` |ASP.NET solicitar prazo de execução |Tempo de execução do mais recente pedido. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET pedidos na fila de execução |Duração da fila de pedidos. |
| `performanceCounter.requests_per_sec.value` |taxa de pedido ASP.NET |Taxa de todos os pedidos para o pedido por segundo a partir de ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Falhas de dependência |Contagem de chamadas falhadas feitas pela aplicação do servidor a recursos externos. |
| `request.duration` |Tempo de resposta do servidor |Tempo entre receber um pedido HTTP e terminar o envio da resposta. |
| `request.rate` |Taxa de pedido |Taxa de todos os pedidos para o pedido por segundo. |
| `requestFailed.count` |Pedidos com falhas |Contagem de pedidos HTTP que resultaram num código de resposta >= 400 |
| `view.count` |Vistas da página |Contagem de pedidos de utilizador do cliente para uma página web. O tráfego sintético é filtrado. |
| {o seu nome métrico personalizado} |{O seu nome métrico} |O seu valor métrico reportado pela [TrackMetric](./api-custom-events-metrics.md#trackmetric) ou no [parâmetro de medição de uma chamada de rastreio](./api-custom-events-metrics.md#properties). |

As métricas são enviadas por diferentes módulos de telemetria:

| Grupo métrico | Módulo de colecionador |
| --- | --- |
| basicExceptionBrowser,<br/>clientePerformance,<br/>ver |[JavaScript do navegador](./javascript.md) |
| performanceCounter |[Desempenho](./configuration-with-applicationinsights-config.md) |
| remotoDependencyFailed |[Dependência](./configuration-with-applicationinsights-config.md) |
| pedido,<br/>pedidosFailed |[Pedido de servidor](./configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Pode [automatizar a sua resposta a um alerta.](../platform/alerts-webhooks.md) O Azure irá ligar para um endereço web à sua escolha quando um alerta for levantado.

## <a name="see-also"></a>Consulte também
* [Script para configurar insights de aplicações](./create-new-resource.md#creating-a-resource-automatically)
* [Criar Insights de Aplicação e recursos de teste web a partir de modelos](powershell.md)
* [Automatizar o acoplamento do Microsoft Azure Diagnostics a Application Insights](powershell-azure-diagnostics.md)
* [automatizar a sua resposta a um alerta](../platform/alerts-webhooks.md)

