---
title: Métricas de Retransmissão Azure em | monitor Azure Microsoft Docs
description: Este artigo fornece informações sobre como pode utilizar o Azure Monitor para monitorizar o estado do Azure Relay.
services: service-bus-relay
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7f91ac0aec46d0fc24901585156dceabb18640c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586833"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Métricas de Retransmissão Azure em Monitor Azure 
As métricas Azure Relay dão-lhe o estado dos recursos na sua assinatura Azure. Com um rico conjunto de dados de métricas, você pode avaliar a saúde geral dos seus recursos De Retransmissor, não só ao nível do espaço de nome, mas também ao nível da entidade. Estas estatísticas podem ser importantes, uma vez que o ajudam a monitorizar o estado de Azure Relay. As métricas também podem ajudar a resolver problemas de causa de raiz sem precisar de contactar o suporte do Azure.

O Azure Monitor fornece interfaces de utilizador unificadas para monitorização em vários serviços Azure. Para obter mais informações, consulte [monitorar no Microsoft Azure](../azure-monitor/overview.md) e nas [métricas do Monitor De Recuperação Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

> [!IMPORTANT]
> Este artigo aplica-se apenas à característica De Conexões Híbridas do Azure Relay, e não ao WCF Relay. 

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias formas de aceder às métricas. Pode aceder às métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e soluções de análise como a Operation Management Suite e os Event Hubs. Para obter mais informações, consulte [os dados de monitorização recolhidos pelo Azure Monitor](../azure-monitor/data-platform.md).

As métricas são ativadas por padrão, e pode aceder aos últimos 30 dias de dados. Se precisar de reter dados por um período de tempo mais longo, pode arquivar dados de métricas numa conta de Armazenamento Azure. Isto está configurado em [definições de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) no Monitor Azure.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar métricas ao longo do tempo no [portal Azure](https://portal.azure.com). O exemplo a seguir mostra como visualizar pedidos bem sucedidos e pedidos de entrada ao nível da conta:

![Uma página intitulada "Monitor - Métricas (pré-visualização)" mostra um gráfico de linha de utilização da memória nos últimos 30 dias.][1]

Também pode aceder às métricas diretamente através do espaço de nomes. Para tal, selecione o seu espaço de nome e, em seguida, clique em **Métricas**. 

Para as métricas que suportam dimensões, deve filtrar com o valor de dimensão pretendido.

## <a name="billing"></a>Faturação

A utilização de métricas no Azure Monitor é atualmente gratuita durante a pré-visualização. No entanto, se utilizar soluções adicionais que ingerem dados de métricas, poderá ser faturado por estas soluções. Por exemplo, é faturado pelo Azure Storage se arquivar dados de métricas numa conta de Armazenamento Azure. Também é faturado por registos do Azure Monitor se transmitir dados de métricas para registos do Monitor Azure para análise avançada.

As seguintes métricas dão-lhe uma visão geral da saúde do seu serviço. 

> [!NOTE]
> Estamos a depreciar várias métricas à medida que são movidas com um nome diferente. Isto pode exigir que atualize as suas referências. As métricas marcadas com a palavra-chave "precedida" não serão suportadas para a frente.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual são apresentados os valores das métricas. O intervalo de tempo suportado para todas as métricas do Relé Azure é de 1 minuto.

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Description |
| ------------------- | ----------------- |
| ListenerConnections-Success  | O número de ligações de ouvintes bem sucedidas efetuadas ao Azure Relay durante um período especificado. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|ListenerConnections-ClientError |O número de erros do cliente nas ligações dos ouvintes durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|ListenerConnections-ServerError |O número de erros do servidor nas ligações do ouvinte durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|SenderConnections-Success |O número de ligações de remetente bem sucedidas efetuadas durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|SenderConnections-ClientError |O número de erros do cliente nas ligações de remetente durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|SenderConnections-ServerError |O número de erros do servidor nas ligações de remetente durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|ListenerConnections-TotalRequests |O número total de ligações de ouvintes durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|SenderConnections-TotalRequests |Os pedidos de ligação feitos pelos remetentes durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|ActiveConnections |O número de ligações ativas. Este valor é um valor pontual.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|ActiveListeners |O número de ouvintes ativos. Este valor é um valor pontual.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|OuvinteSEsa ligações |O número de ouvintes desligados durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|
|SenderDis ligações |O número de remetentes desligados durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|

## <a name="memory-usage-metrics"></a>Métricas de utilização da memória

| Nome da Métrica | Description |
| ------------------- | ----------------- |
|BytesTransferido |O número de bytes transferidos durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome da Entidade|

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Azure Relay suporta as seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões às suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas ao nível do espaço de identificação. 

|Nome de dimensão|Description|
| ------------------- | ----------------- |
|Nome de Entidade| A Azure Relay suporta entidades de mensagens sob o espaço de nome.|

## <a name="next-steps"></a>Passos seguintes

Consulte a [visão geral da Monitorização do Azure](../azure-monitor/overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png
