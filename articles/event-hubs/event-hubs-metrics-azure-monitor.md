---
title: Métricas em Azure Monitor-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como utilizar a monitorização do Azure para monitorizar os Hubs de eventos do Azure
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 788f0647bec11184c2a85d87d0dfde2cb6c5744c
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266309"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Métricas dos hubs de eventos do Azure no Azure Monitor

As métricas dos hubs de eventos fornecem o estado dos recursos dos hubs de eventos em sua assinatura do Azure. Com um vasto conjunto de dados de métricas, pode avaliar o estado de funcionamento geral os hubs de eventos não apenas ao nível do espaço de nomes, mas também no nível de entidade. Essas estatísticas podem ser importantes como eles ajudam-na monitorizar o estado dos hubs de eventos. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para obter mais informações, consulte [monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o [métricas de obter o Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) exemplo no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Você pode acessar as métricas por meio do [portal do Azure](https://portal.azure.com)ou usar as APIs de Azure monitor (REST e .net) e as soluções de análise, como log Analytics e hubs de eventos. Para obter mais informações, consulte [dados de monitorização recolhidos pelo Azure Monitor](../azure-monitor/platform/data-platform.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [das definições de diagnóstico](../azure-monitor/platform/diagnostic-settings.md) no Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo na [portal do Azure](https://portal.azure.com). O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![Ver métricas de sucesso][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para fazer isso, selecione o namespace e clique em **métricas**. Para exibir as métricas filtradas para o escopo do hub de eventos, selecione o Hub de eventos e clique em **métricas**.

Para as métricas que suporta dimensões, tem de filtrar com o valor de dimensão pretendida, conforme mostrado no exemplo a seguir:

![Filtrar com valor de dimensão][2]

## <a name="billing"></a>Faturação

O uso de métricas no Azure Monitor está livre no momento. No entanto, se utilizar soluções adicionais que ingerir dados de métricas, pode ser cobrado por estas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Você também será cobrado pelo Azure se você transmitir dados de métricas para Azure Monitor logs para análise avançada.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas de Hubs de eventos é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedidos

Conta o número de pedidos de operações de gestão e de dados.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Pedidos Recebidos  | O número de pedidos efetuados para o serviço de Event Hubs do Azure num determinado período. <br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName |
| Pedidos Com Êxito    | O número de pedidos com êxito efetuados para o serviço de Event Hubs do Azure num determinado período. <br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName |
| Erros no Servidor  | O número de pedidos não processadas devido a um erro no serviço de Event Hubs do Azure num determinado período. <br/><br/>Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName |
|Erros do usuário |O número de pedidos não processadas devido a erros de utilizador num determinado período.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Erros de Cota excedida |O número de pedidos excedeu a quota disponível. Ver [este artigo](event-hubs-quotas.md) para obter mais informações sobre as quotas de Hubs de eventos.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|

## <a name="throughput-metrics"></a>Métricas de débito

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Solicitações limitadas |O número de pedidos que foram limitado porque a utilização de unidades de débito foi excedida.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|

## <a name="message-metrics"></a>Métricas de mensagem

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens Recebidas |O número de eventos ou as mensagens enviadas para os Hubs de eventos num determinado período.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Mensagens Enviadas |Obter o número de mensagens ou eventos dos Hubs de eventos num determinado período.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Bytes de entrada |O número de bytes enviados para o serviço de Event Hubs do Azure num determinado período.<br/><br/> Unidade Bytes <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Bytes de saída |O número de bytes obtidos a partir do serviço de Event Hubs do Azure num determinado período.<br/><br/> Unidade Bytes <br/> Tipo de agregação: Total <br/> Dimensões EntityName|

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections |O número de ligações ativas num espaço de nomes, bem como numa entidade.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Conexões abertas |O número de conexões abertas.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Conexões fechadas |O número de conexões encerradas.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|

## <a name="event-hubs-capture-metrics"></a>Métricas de captura dos Hubs de eventos

Pode monitorizar métricas de captura de Hubs de eventos quando ativar a funcionalidade de captura para os hubs de eventos. As métricas seguintes descrevem o que pode monitorizar com a captura ativada.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Capturar registro posterior |O número de bytes que ainda estão a ser capturadas para o destino escolhido.<br/><br/> Unidade Bytes <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Mensagens capturadas |O número de mensagens ou eventos que são capturados para o destino escolhido num determinado período.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Bytes capturados |O número de bytes que são capturados para o destino escolhido num determinado período.<br/><br/> Unidade Bytes <br/> Tipo de agregação: Total <br/> Dimensões EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

Os Hubs de eventos do Azure suporta as seguintes dimensões de métricas no Azure Monitor. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|EntityName| Os Hubs de eventos suporta as entidades do hub de eventos sob o espaço de nomes.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Integração do Azure Monitor com as ferramentas SIEM
O roteamento dos dados de monitoramento (logs de atividades, logs de diagnóstico, etc.) para um hub de eventos com o Azure Monitor permite que você integre facilmente as ferramentas SIEM (gerenciamento de eventos e informações de segurança). Para obter mais informações, consulte os seguintes artigos/Postagens de blog:

- [Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Introdução à integração de log do Azure](../security/fundamentals/azure-log-integration-overview.md)
- [Use Azure Monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Utilizar o Azure Monitor para integrar com ferramentas de SIEM)

No cenário em que uma ferramenta SIEM consome dados de log de um hub de eventos, se você não vir mensagens de entrada ou se vir mensagens de entrada, mas nenhuma mensagem de saída no grafo de métricas, siga estas etapas:

- Se não houver **nenhuma mensagem de entrada**, significa que o serviço de Azure monitor não está movendo os logs de auditoria/diagnóstico para o Hub de eventos. Abra um tíquete de suporte com a equipe de Azure Monitor neste cenário. 
- Se houver mensagens de entrada, mas **nenhuma mensagem de saída**, isso significa que o aplicativo Siem não está lendo as mensagens. Entre em contato com o provedor SIEM para determinar se a configuração do hub de eventos desses aplicativos está correta.


## <a name="next-steps"></a>Passos seguintes

* Consulte a [descrição geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* [Obter métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) exemplo no GitHub. 

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



