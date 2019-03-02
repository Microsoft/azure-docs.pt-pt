---
title: Métricas de reencaminhamento do Azure no Azure Monitor (pré-visualização) | Documentos da Microsoft
description: Utilizar a monitorização do Azure para monitorizar o reencaminhamento do Azure
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
ms.date: 11/28/2018
ms.author: spelluru
ms.openlocfilehash: ebff47dc4007ab265b43d32c9f956eb73d5ba1ac
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213420"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Métricas de reencaminhamento do Azure no Azure Monitor (pré-visualização)
Métricas de reencaminhamento do Azure dão-lhe o estado dos recursos na sua subscrição do Azure. Com um vasto conjunto de dados de métricas, pode avaliar o estado de funcionamento geral dos seus recursos de reencaminhamento, não apenas ao nível do espaço de nomes, mas também no nível de entidade. Essas estatísticas podem ser importantes como eles ajudam-na monitorizar o estado do reencaminhamento do Azure. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para obter mais informações, consulte [monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o [métricas de obter o Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) exemplo no GitHub.

> [!IMPORTANT]
> Este artigo aplica-se apenas a funcionalidade ligações híbridas do reencaminhamento do Azure, não para o reencaminhamento de WCF. 

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Pode qualquer uma das métricas de acesso através da [portal do Azure](https://portal.azure.com), ou utilize o Azure Monitor APIs (REST e .NET) e soluções de análise de como o Operation Management Suite e dos Hubs de eventos. Para obter mais informações, consulte [dados de monitorização recolhidos pelo Azure Monitor](../azure-monitor/platform/data-collection.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [das definições de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo na [portal do Azure](https://portal.azure.com). O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para tal, selecione o seu espaço de nomes e, em seguida, clique em **métricas (pré-visualização)**. 

Para as métricas que suporta dimensões, tem de filtrar com o valor de dimensão pretendida.

## <a name="billing"></a>Faturação

Uso de métricas no Azure Monitor é atualmente gratuito enquanto está em pré-visualização. No entanto, se utilizar soluções adicionais que ingerir dados de métricas, pode ser cobrado por estas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Também são cobradas pelos registos do Azure Monitor se os transmitir dados de métricas para os registos do Azure Monitor para análises avançadas.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas de reencaminhamento do Azure é de 1 minuto.

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| ListenerConnections-êxito (pré-visualização) | O número de conexões com êxito do serviço de escuta a reencaminhamento do Azure num determinado período. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ClientError (pré-visualização)|O número de erros do cliente em ligações de serviço de escuta num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ServerError (pré-visualização)|O número de erros de servidor em ligações de serviço de escuta num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-êxito (pré-visualização)|O número de ligações de remetente bem-sucedida efetuadas num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ClientError (pré-visualização)|O número de erros do cliente em ligações de remetente num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ServerError (pré-visualização)|O número de erros de servidor em ligações de remetente num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-TotalRequests (pré-visualização)|O número total de ligações de serviço de escuta num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-TotalRequests (pré-visualização)|Os pedidos de ligação graças as remetentes num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveConnections (pré-visualização)|O número de ligações ativas num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveListeners (pré-visualização)|O número de serviços de escuta ativos durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerDisconnects (pré-visualização)|O número de serviços de escuta desligado num determinado período.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderDisconnects (pré-visualização)|O número de remetentes desligados num determinado período.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="memory-usage-metrics"></a>Métrica de utilização de memória

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|BytesTransferred (pré-visualização)|O número de bytes transferidos por um período de tempo especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

Reencaminhamento do Azure suporta as seguintes dimensões de métricas no Azure Monitor. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

|Nome da dimensão|Descrição|
| ------------------- | ----------------- |
|EntityName| Reencaminhamento do Azure oferece suporte a entidades de mensagens sob o espaço de nomes.|

## <a name="next-steps"></a>Passos Seguintes

Consulte a [descrição geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




