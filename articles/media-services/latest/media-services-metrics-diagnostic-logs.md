---
title: Métricas de Serviços de Mídia e registos de diagnóstico com O Azure Monitor
titleSuffix: Azure Media Services
description: Saiba como monitorizar as métricas dos Serviços de Media Azure e os registos de diagnóstico através do Azure Monitor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c03950d64c9ead17dfa5c07ef70ab2b7ee0e90bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296656"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Monitorize métricas de Serviços de Mídia e registos de diagnóstico via Azure Monitor

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[O Azure Monitor](../../azure-monitor/overview.md) permite monitorizar métricas e registos de diagnóstico que o ajudam a entender como as suas aplicações estão a funcionar. Todos os dados recolhidos pelo Azure Monitor enquadram-se num de dois tipos fundamentais: métricas e registos. Pode monitorizar os registos de diagnóstico dos Serviços de Comunicação Social e criar alertas e notificações para as métricas e registos recolhidos. Pode visualizar e analisar os dados das métricas utilizando o [explorador métrica.](../../azure-monitor/platform/metrics-getting-started.md) Pode enviar registos para [o Azure Storage,](https://azure.microsoft.com/services/storage/)transmiti-los para [Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/)exportá-los para [Log Analytics](https://azure.microsoft.com/services/log-analytics/)ou utilizar serviços de terceiros.

Para obter uma visão geral detalhada, consulte [as métricas do Monitor Azure](../../azure-monitor/platform/data-platform.md) e [os registos de diagnóstico do Monitor Azure](../../azure-monitor/platform/platform-logs-overview.md).

Este tópico discute registos de diagnóstico de serviços de [mídia](#media-services-metrics) [apoiados.](#media-services-diagnostic-logs)

## <a name="media-services-metrics"></a>Métricas de Serviços de Mídia

As métricas são recolhidas a intervalos regulares, quer o valor mude ou não. São úteis para alertar porque podem ser amostrados com frequência, e um alerta pode ser disparado rapidamente com uma lógica relativamente simples. Para obter informações sobre como criar alertas métricos, consulte [Criar, ver e gerir alertas métricos utilizando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Os Serviços de Comunicação Social apoiam métricas de monitorização dos seguintes recursos:

* Conta
* Ponto Final de Transmissão em Fluxo

### <a name="account"></a>Conta

Pode monitorizar as seguintes métricas de conta.

|Nome da métrica|Nome a apresentar|Descrição|
|---|---|---|
|Conta de Ativos|Contagem de ativos|Bens na sua conta.|
|AssetQuota|Quota de ativos|Quota de ativo na sua conta.|
|AssetQuotaUsedPercentage|Percentagem de quota de ativos utilizada|A percentagem da quota de ativos já utilizada.|
|ContentKeyPolicyCount|Contagem de políticas de chave de conteúdo|Políticas chave de conteúdo na sua conta.|
|ContentKeyPolicyQuota|Quota de política chave de conteúdo|Contingente de Políticas Chave de Conteúdo na sua conta.|
|ContentKeyPolicyQuotaUsedPercentage|Quota de política de conteúdo utilizada|A percentagem da quota de política de chave de conteúdo já utilizada.|
|StreamingPolicyCount|Contagem de políticas de streaming|Políticas de streaming na sua conta.|
|StreamingPolicyQuota|Quota de política de streaming|Quota de Políticas de Streaming na sua conta.|
|StreamingPolicyQuotaUsedPercentage|Percentagem utilizada pela quota de política de streaming|A percentagem da quota da Política de Streaming já utilizada.|

Deve também rever [as quotas e limites de conta.](limits-quotas-constraints.md)

### <a name="streaming-endpoint"></a>Ponto Final de Transmissão em Fluxo

São suportadas as [seguintes métricas de serviços](/rest/api/media/streamingendpoints) de streaming de media:

|Nome da métrica|Nome a apresentar|Descrição|
|---|---|---|
|Pedidos|Pedidos|Fornece o número total de pedidos HTTP servidos pelo Streaming Endpoint.|
|Saída|Saída|Egress bytes total por minuto por streaming Endpoint.|
|SucessoE2ELatency|Fim do sucesso para acabar com a latência|Duração do tempo a partir do momento em que o Streaming Endpoint recebeu o pedido para quando foi enviado o último byte da resposta.|

### <a name="why-would-i-want-to-use-metrics"></a>Por que iria querer usar métricas?

Aqui estão exemplos de como monitorizar as métricas dos Media Services pode ajudá-lo a entender como as suas aplicações estão a funcionar. Algumas questões que podem ser abordadas com as métricas dos Serviços de Comunicação Social são:

* Como monitorizo o meu Standard Streaming Endpoint para saber quando ultrapassei os limites?
* Como é que eu sei se tenho unidades de escala de endpoint de streaming premium suficientes?
* Como posso definir um alerta para saber quando aumentar os meus Pontos Finais de Streaming?
* Como posso alertar para saber quando a saída máxima configurada na conta foi atingida?
* Como posso ver a quebra de pedidos a falhar e o que está a causar a falha?
* Como posso ver quantos pedidos de HLS ou DASH estão a ser retirados do embalador?
* Como posso definir um alerta para saber quando o valor limiar de # de pedidos falhados foi atingido?

### <a name="example"></a>Exemplo

Ver [Como monitorizar as métricas dos Serviços de Comunicação](media-services-metrics-howto.md)Social.

## <a name="media-services-diagnostic-logs"></a>Registos de diagnóstico dos Serviços de Comunicação Social

Os registos de diagnóstico fornecem dados ricos e frequentes sobre o funcionamento de um recurso Azure. Para mais informações, consulte [Como recolher e consumir dados de registo dos seus recursos Azure.](../../azure-monitor/platform/platform-logs-overview.md)

Os Serviços de Comunicação Social suportam os seguintes registos de diagnóstico:

* Entrega chave

### <a name="key-delivery"></a>Entrega chave

|Nome|Descrição|
|---|---|
|Pedido de serviço de entrega chave|Registos que mostram a informação do serviço de entrega chave. Para mais informações, consulte [os esquemas.](media-services-diagnostic-logs-schema.md)|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Por que iria querer usar registos de diagnóstico?

Algumas coisas que pode examinar com os principais registos de diagnóstico de entrega são:

* Consulte o número de licenças entregues por tipo DRM.
* Consulte o número de licenças entregues por apólice.
* Consulte erros por DRM ou tipo de política.
* Consulte o número de pedidos de licença não autorizados de clientes.

### <a name="example"></a>Exemplo

Ver [como monitorizar os registos de diagnóstico do Media Service](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Passos seguintes

* [Como recolher e consumir dados de registo dos seus recursos Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Criar, ver e gerir alertas de métricas com o Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Como monitorizar as métricas dos Serviços de Comunicação Social](media-services-metrics-howto.md)
* [Como monitorizar registos de diagnóstico do Media Service](media-services-diagnostic-logs-howto.md)
