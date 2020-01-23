---
title: Métricas de retransmissão do Azure no Azure Monitor (visualização) | Microsoft Docs
description: Este artigo fornece informações sobre como você pode usar Azure Monitor para monitorar o estado da retransmissão do Azure.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 5c548186ec51cf86f34942cb15d8f984afa60268
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514940"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Métricas de retransmissão do Azure no Azure Monitor (versão prévia)
As métricas de retransmissão do Azure fornecem o estado dos recursos em sua assinatura do Azure. Com um rico conjunto de dados de métricas, você pode avaliar a integridade geral dos recursos de retransmissão, não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois ajudam você a monitorar o estado da retransmissão do Azure. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para obter mais informações, consulte [monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o [métricas de obter o Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) exemplo no GitHub.

> [!IMPORTANT]
> Este artigo aplica-se somente ao recurso Conexões Híbridas da retransmissão do Azure, não ao Retransmissão do WCF. 

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Pode qualquer uma das métricas de acesso através da [portal do Azure](https://portal.azure.com), ou utilize o Azure Monitor APIs (REST e .NET) e soluções de análise de como o Operation Management Suite e dos Hubs de eventos. Para obter mais informações, consulte [dados de monitorização recolhidos pelo Azure Monitor](../azure-monitor/platform/data-platform.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [das definições de diagnóstico](../azure-monitor/platform/diagnostic-settings.md) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo na [portal do Azure](https://portal.azure.com). O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para fazer isso, selecione o namespace e clique em **métricas (versão prévia)** . 

Para métricas com suporte a dimensões, você deve filtrar com o valor de dimensão desejado.

## <a name="billing"></a>Faturação

Uso de métricas no Azure Monitor é atualmente gratuito enquanto está em pré-visualização. No entanto, se utilizar soluções adicionais que ingerir dados de métricas, pode ser cobrado por estas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Você também será cobrado por logs de Azure Monitor se transmitir dados de métricas para Azure Monitor logs para análise avançada.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas de retransmissão do Azure é de 1 minuto.

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| ListenerConnections-êxito (versão prévia) | O número de conexões de ouvinte bem-sucedidas feitas para a retransmissão do Azure durante um período especificado. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ClientError (versão prévia)|O número de erros do cliente em conexões de ouvinte durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ServerError (versão prévia)|O número de erros de servidor no ouvinte de conexões durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-êxito (versão prévia)|O número de conexões de remetente bem-sucedidas feitas em um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ClientError (versão prévia)|O número de erros de cliente em conexões de remetente durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ServerError (versão prévia)|O número de erros de servidor em conexões de remetente durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-TotalRequests (versão prévia)|O número total de conexões de ouvinte em um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-TotalRequests (versão prévia)|As solicitações de conexão feitas pelos remetentes durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveConnections (pré-visualização)|O número de conexões ativas em um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveListeners (visualização)|O número de ouvintes ativos em um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerDisconnects (visualização)|O número de ouvintes desconectados durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderDisconnects (visualização)|O número de remetentes desconectados em um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="memory-usage-metrics"></a>Métricas de uso de memória

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|BytesTransferred (visualização)|O número de bytes transferidos durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

A retransmissão do Azure dá suporte às seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

|Nome da dimensão|Descrição|
| ------------------- | ----------------- |
|EntityName| A retransmissão do Azure dá suporte a entidades de mensagens no namespace.|

## <a name="next-steps"></a>Passos seguintes

Consulte a [descrição geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




