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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273122"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Métricas de Retransmissão Azure no Monitor Azure 
As métricas Azure Relay dão-lhe o estado dos recursos na sua subscrição Azure. Com um conjunto rico de dados de métricas, você pode avaliar a saúde geral dos seus recursos Relay, não só ao nível do espaço de nome, mas também ao nível da entidade. Estas estatísticas podem ser importantes porque o ajudam a monitorizar o estado de Azure Relay. As métricas também podem ajudar a resolver problemas de causa sem necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces unificadas de utilizador para monitorização em vários serviços Do Azure. Para mais informações, consulte [a monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e nas [métricas do Monitor Retrieve Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

> [!IMPORTANT]
> Este artigo aplica-se apenas à funcionalidade ligações híbridas do Relé Azure, e não ao Relé WCF. 

## <a name="access-metrics"></a>Métricas de acesso

O Monitor Azure fornece várias formas de aceder às métricas. Pode aceder a métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e soluções de análise como a Operation Management Suite e os Event Hubs. Para mais informações, consulte [os dados de Monitorização recolhidos pelo Monitor Azure](../azure-monitor/platform/data-platform.md).

As métricas são ativadas por padrão, e você pode aceder aos mais recentes 30 dias de dados. Se precisar de reter dados por um período mais longo, pode arquivar dados de métricas numa conta de Armazenamento Azure. Isto está configurado em [definições](../azure-monitor/platform/diagnostic-settings.md) de diagnóstico no Monitor Azure.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo no [portal Azure.](https://portal.azure.com) O exemplo que se segue mostra como visualizar pedidos bem sucedidos e pedidos de entrada ao nível da conta:

![][1]

Também pode aceder às métricas diretamente através do espaço de nome. Para isso, selecione o seu espaço de nome e, em seguida, clique em **Metrics **. 

Para métricas que suportem dimensões, deve filtrar com o valor de dimensão desejado.

## <a name="billing"></a>Faturação

A utilização de métricas no Monitor Azure é atualmente gratuita durante a pré-visualização. No entanto, se utilizar soluções adicionais que ingerirem dados de métricas, poderá ser faturado por estas soluções. Por exemplo, é cobrado pelo Azure Storage se arquivar dados de métricas para uma conta de Armazenamento Azure. Também é cobrado por registos do Monitor Azure se transmitir dados de métricas para registos do Monitor Azure para análise supérflua.

As seguintes métricas dão-lhe uma visão geral da saúde do seu serviço. 

> [!NOTE]
> Estamos a depreciar várias métricas à medida que são movidas com um nome diferente. Isto pode exigir que atualize as suas referências. As métricas marcadas com a palavra-chave "depreciada" não serão suportadas para avançar.

Todos os valores das métricas são enviados para o Monitor Azure a cada minuto. A granularidade do tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas do Relé Azure é de 1 minuto.

## <a name="connection-metrics"></a>Métricas de ligação

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| ListenerConnections-Success  | O número de ligações bem sucedidas de ouvintes feitas ao Azure Relay durante um período determinado. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|ListenerConnections-ClientError |O número de erros do cliente nas ligações com ouvintes durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|ListenerConnections-ServerError |O número de erros do servidor nas ligações do ouvinte durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|SenderConnections-Success |O número de ligações bem sucedidas do remetente efetuadas durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|SenderConnections-ClientError |O número de erros do cliente nas ligações do remetente durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|SenderConnections-ServerError |O número de erros do servidor nas ligações do remetente durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|ListenerConnections-TotalRequests |O número total de ligações ouvintes durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|SenderConnections-TotalRequests |Os pedidos de ligação feitos pelos remetentes durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|ActiveConnections |O número de ligações ativas. Este valor é um valor pontual.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Ouvintes Ativos |O número de ouvintes ativos. Este valor é um valor pontual.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|ListenerDisconnects |O número de ouvintes desligados durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|SenderDisconnects |O número de remetentes desligados durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

## <a name="memory-usage-metrics"></a>Métricas de utilização da memória

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|BytesTransferido |O número de bytes transferidos durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

## <a name="metrics-dimensions"></a>Dimensões métricas

O Azure Relay suporta as seguintes dimensões para métricas no Monitor Azure. Adicionar dimensões às suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas ao nível do espaço de nome. 

|Nome de dimensão|Descrição|
| ------------------- | ----------------- |
|Nome da entidade| A Azure Relay apoia entidades de mensagens sob o espaço de nome.|

## <a name="next-steps"></a>Passos seguintes

Consulte a visão geral da [Monitorização Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




