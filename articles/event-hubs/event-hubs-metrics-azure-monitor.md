---
title: Métricas no Azure Monitor - Hubs de eventos do Azure | Documentos da Microsoft
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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d273e963c6191fdc87c4f19c6465d8a9026a074e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959413"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Métricas de Hubs de eventos do Azure no Azure Monitor

Métricas de Hubs de eventos-lhe o estado dos recursos de Hubs de eventos na sua subscrição do Azure. Com um vasto conjunto de dados de métricas, pode avaliar o estado de funcionamento geral os hubs de eventos não apenas ao nível do espaço de nomes, mas também no nível de entidade. Essas estatísticas podem ser importantes como eles ajudam-na monitorizar o estado dos hubs de eventos. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para obter mais informações, consulte [monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o [métricas de obter o Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) exemplo no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Pode qualquer uma das métricas de acesso através da [portal do Azure](https://portal.azure.com), ou utilize o Azure Monitor APIs (REST e .NET) e soluções de análise de como o Operation Management Suite e dos Hubs de eventos. Para obter mais informações, consulte [dados de monitorização recolhidos pelo Azure Monitor](../azure-monitor/platform/data-collection.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [das definições de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo na [portal do Azure](https://portal.azure.com). O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![Ver métricas de sucesso][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para tal, selecione o seu espaço de nomes e, em seguida, clique em * * métricas * *. Para apresentar métricas filtradas para o escopo do hub de eventos, selecione o hub de eventos e, em seguida, clique em **métricas**.

Para as métricas que suporta dimensões, tem de filtrar com o valor de dimensão pretendida, conforme mostrado no exemplo a seguir:

![Filtrar com valor de dimensão][2]

## <a name="billing"></a>Faturação

Uso de métricas no Azure Monitor é atualmente gratuito. No entanto, se utilizar soluções adicionais que ingerir dados de métricas, pode ser cobrado por estas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Também são cobradas pelo Azure se os transmitir dados de métricas para os registos do Azure Monitor para análises avançadas.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas de Hubs de eventos é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedidos

Conta o número de pedidos de operações de gestão e de dados.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Pedidos de entrada (pré-visualização) | O número de pedidos efetuados para o serviço de Event Hubs do Azure num determinado período. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Pedidos com êxito (pré-visualização)   | O número de pedidos com êxito efetuados para o serviço de Event Hubs do Azure num determinado período. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Erros de servidor (pré-visualização) | O número de pedidos não processadas devido a um erro no serviço de Event Hubs do Azure num determinado período. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
|Erros de utilizador (pré-visualização)|O número de pedidos não processadas devido a erros de utilizador num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Quota excedida erros (pré-visualização)|O número de pedidos excedeu a quota disponível. Ver [este artigo](event-hubs-quotas.md) para obter mais informações sobre as quotas de Hubs de eventos.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="throughput-metrics"></a>Métricas de débito

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Pedidos limitados (pré-visualização)|O número de pedidos que foram limitado porque a utilização de unidades de débito foi excedida.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="message-metrics"></a>Métricas de mensagem

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens de entrada (pré-visualização)|O número de eventos ou as mensagens enviadas para os Hubs de eventos num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída (pré-visualização)|Obter o número de mensagens ou eventos dos Hubs de eventos num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes recebidos (pré-visualização)|O número de bytes enviados para o serviço de Event Hubs do Azure num determinado período.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes enviados (pré-visualização)|O número de bytes obtidos a partir do serviço de Event Hubs do Azure num determinado período.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections (pré-visualização)|O número de ligações ativas num espaço de nomes, bem como numa entidade.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Ligações aberto (pré-visualização)|O número de conexões abertas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Ligações fechado (pré-visualização)|O número de conexões encerradas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="event-hubs-capture-metrics"></a>Métricas de captura dos Hubs de eventos

Pode monitorizar métricas de captura de Hubs de eventos quando ativar a funcionalidade de captura para os hubs de eventos. As métricas seguintes descrevem o que pode monitorizar com a captura ativada.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Capturar o registo de segurança (pré-visualização)|O número de bytes que ainda estão a ser capturadas para o destino escolhido.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens recolhidas (pré-visualização)|O número de mensagens ou eventos que são capturados para o destino escolhido num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes recolhidos (pré-visualização)|O número de bytes que são capturados para o destino escolhido num determinado período.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

Os Hubs de eventos do Azure suporta as seguintes dimensões de métricas no Azure Monitor. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|EntityName| Os Hubs de eventos suporta as entidades do hub de eventos sob o espaço de nomes.|

## <a name="next-steps"></a>Passos Seguintes

* Consulte a [descrição geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* [Obter métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) exemplo no GitHub. 

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



