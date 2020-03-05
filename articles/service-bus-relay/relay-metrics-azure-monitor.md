---
title: Métricas de Retransmissão Azure no Monitor Azure / Microsoft Docs
description: Este artigo fornece informações sobre como pode usar o Monitor Azure para monitorizar o estado do Azure Relay.
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
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273122"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Métricas de Retransmissão Azure no Monitor Azure 
As métricas Azure Relay dão-lhe o estado dos recursos na sua subscrição Azure. Com um conjunto rico de dados de métricas, você pode avaliar a saúde geral dos seus recursos Relay, não só ao nível do espaço de nome, mas também ao nível da entidade. Estas estatísticas podem ser importantes porque o ajudam a monitorizar o estado de Azure Relay. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para mais informações, consulte [a monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e nas [métricas do Monitor Retrieve Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

> [!IMPORTANT]
> Este artigo aplica-se apenas à funcionalidade ligações híbridas do Relé Azure, e não ao Relé WCF. 

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Pode aceder a métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e soluções de análise como a Operation Management Suite e os Event Hubs. Para mais informações, consulte [os dados de Monitorização recolhidos pelo Monitor Azure](../azure-monitor/platform/data-platform.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Isto está configurado em [definições](../azure-monitor/platform/diagnostic-settings.md) de diagnóstico no Monitor Azure.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo no [portal Azure.](https://portal.azure.com) O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para isso, selecione o seu espaço de nome e, em seguida, clique em **Metrics **. 

Para métricas que suportem dimensões, deve filtrar com o valor de dimensão desejado.

## <a name="billing"></a>Faturação

Uso de métricas no Azure Monitor é atualmente gratuito enquanto está em pré-visualização. No entanto, se utilizar soluções adicionais que ingerir dados de métricas, pode ser cobrado por estas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Também é cobrado por registos do Monitor Azure se transmitir dados de métricas para registos do Monitor Azure para análise supérflua.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas do Relé Azure é de 1 minuto.

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| ListenerConnections-Success  | O número de ligações bem sucedidas de ouvintes feitas ao Azure Relay durante um período determinado. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ClientError |O número de erros do cliente nas ligações com ouvintes durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ServerError |O número de erros do servidor nas ligações do ouvinte durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-Success |O número de ligações bem sucedidas do remetente efetuadas durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ClientError |O número de erros do cliente nas ligações do remetente durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ServerError |O número de erros do servidor nas ligações do remetente durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-TotalRequests |O número total de ligações ouvintes durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-TotalRequests |Os pedidos de ligação feitos pelos remetentes durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveConnections |O número de ligações ativas. Este valor é um valor pontual.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Ouvintes Ativos |O número de ouvintes ativos. Este valor é um valor pontual.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerDisconnects |O número de ouvintes desligados durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderDisconnects |O número de remetentes desligados durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="memory-usage-metrics"></a>Métricas de utilização da memória

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|BytesTransferido |O número de bytes transferidos durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

O Azure Relay suporta as seguintes dimensões para métricas no Monitor Azure. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

|Nome de dimensão|Descrição|
| ------------------- | ----------------- |
|EntityName| A Azure Relay apoia entidades de mensagens sob o espaço de nome.|

## <a name="next-steps"></a>Passos seguintes

Consulte a visão geral da [Monitorização Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




