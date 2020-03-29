---
title: Métricas de Serviços de Media e registos de diagnóstico com o Monitor Azure
titleSuffix: Azure Media Services
description: Saiba como monitorizar as métricas dos Serviços De Mídia Azure e os registos de diagnóstico através do Monitor Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: f075362f976e6abb26c9781c4b0cdeb7912c0862
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514039"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Monitor Media Services métricas e registos de diagnóstico via Azure Monitor

[O Azure Monitor](../../azure-monitor/overview.md) permite-lhe monitorizar métricas e registos de diagnóstico que o ajudam a compreender como as suas aplicações estão a funcionar. Todos os dados recolhidos pelo Azure Monitor enquadram-se num de dois tipos fundamentais: métricas e registos. Pode monitorizar os registos de diagnóstico dos Serviços de Media e criar alertas e notificações para as métricas e registos recolhidos. Pode visualizar e analisar os dados das métricas usando o explorador de [Métricas.](../../azure-monitor/platform/metrics-getting-started.md) Pode enviar registos para [o Azure Storage,](https://azure.microsoft.com/services/storage/)transmiti-los para os Hubs de [Eventos Azure,](https://azure.microsoft.com/services/event-hubs/)exportá-los para [Log Analytics,](https://azure.microsoft.com/services/log-analytics/)ou utilizar serviços de terceiros.

Para uma visão geral detalhada, consulte as Métricas de Diagnóstico [do Monitor Azure](../../azure-monitor/platform/data-platform.md) e do Monitor [Azure](../../azure-monitor/platform/platform-logs-overview.md).

Este tópico discute [registos](#media-services-metrics) de diagnóstico de métricas de serviços de mídia suportados e [de serviços de mídia.](#media-services-diagnostic-logs)

## <a name="media-services-metrics"></a>Métricas dos Serviços de Media

As métricas são recolhidas em intervalos regulares, quer o valor mude ou não. São úteis para alertar porque podem ser amostrados com frequência, e um alerta pode ser disparado rapidamente com uma lógica relativamente simples. Para obter informações sobre como criar alertas métricos, consulte [Criar, visualizar e gerir alertas métricos utilizando o Monitor Azure](../../azure-monitor/platform/alerts-metric.md).

Os Serviços de Media suportam métricas de monitorização para os seguintes recursos:

* Conta
* Ponto Final de Transmissão em Fluxo

### <a name="account"></a>Conta

Pode monitorizar as seguintes métricas de conta.

|Nome da métrica|Nome a apresentar|Descrição|
|---|---|---|
|Contaque de ativos|Contagem de ativos|Bens na sua conta.|
|AssetQuota|Quota de ativos|Quota de ativos na sua conta.|
|Percentagem de Quotas de Ativos|Percentagem de quota de ativos utilizada|A percentagem da quota De ativo já utilizada.|
|Contagem de políticas de conteúdo|Contagem de políticas de chave de conteúdo|Políticas chave de conteúdo na sua conta.|
|ContentKeyPolicyQuota|Quota de Política chave de conteúdo|Quota de Políticas chave de conteúdo na sua conta.|
|ContentKeyPolicyQuotaUsedPercentage|Percentagem de política-chave de conteúdo utilizada|A percentagem da quota de política de conteúdo já utilizada.|
|Contagem de Políticas de Streaming|Contagem de políticas de streaming|Políticas de streaming na sua conta.|
|StreamingPolicyQuota|Quota política de streaming|Quota de Políticas de Streaming na sua conta.|
|StreamingPolicyQuotaUsedPercentage|Quota de política de streaming utilizada percentagem|A percentagem da quota política de streaming já utilizada.|

Deve também rever [as quotas e limitações das contas.](limits-quotas-constraints.md)

### <a name="streaming-endpoint"></a>Ponto Final de Transmissão em Fluxo

As seguintes métricas de [streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) de serviços de mídia são suportadas:

|Nome da métrica|Nome a apresentar|Descrição|
|---|---|---|
|Pedidos|Pedidos|Fornece o número total de pedidos HTTP servidos pelo Streaming Endpoint.|
|Saída|Saída|O número total de bytes de egress. Por exemplo, bytes transmitidos pelo Streaming Endpoint.|
|SuccessE2ELatency|Fim do sucesso para acabar com a Latência|Duração do tempo a partir do momento em que o Ponto final de streaming recebeu o pedido para quando o último byte da resposta foi enviado.|

### <a name="why-would-i-want-to-use-metrics"></a>Por que haveria de querer usar métricas?

Aqui estão exemplos de como monitorizar as métricas dos Serviços de Media pode ajudá-lo a entender como as suas aplicações estão a funcionar. Algumas questões que podem ser abordadas com métricas dos Serviços de Media são:

* Como posso monitorizar o meu Ponto Final de Streaming Padrão para saber quando excedi os limites?
* Como sei se tenho unidades de escala de streaming premium suficientes?
* Como posso definir um alerta para saber quando aumentar os meus pontos finais de streaming?
* Como posso definir um alerta para saber quando a saída máxima configurada na conta foi alcançada?
* Como posso ver a repartição dos pedidos falhar e o que está a causar a falha?
* Como posso ver quantos pedidos de HLS ou DASH estão a ser retirados do embalador?
* Como posso definir um alerta para saber quando o valor limiar de # de pedidos falhados foi atingido?

### <a name="example"></a>Exemplo

Ver [Como monitorizar as métricas dos Serviços de Media](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Registos de diagnóstico dos Serviços de Media

Os registos de diagnóstico fornecem dados ricos e frequentes sobre o funcionamento de um recurso Azure. Para mais informações, consulte [Como recolher e consumir dados de registo dos seus recursos Azure](../../azure-monitor/platform/platform-logs-overview.md).

Os Serviços de Media suportam os seguintes registos de diagnóstico:

* Entrega de chaves

### <a name="key-delivery"></a>Entrega de chaves

|Nome|Descrição|
|---|---|
|Pedido de serviço de entrega chave|Registos que mostram a informação de pedido de pedido de serviço de entrega de chaves. Para mais informações, consulte [schemas.](media-services-diagnostic-logs-schema.md)|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Por que gostaria de usar registos de diagnóstico?

Algumas coisas que pode examinar com registos de diagnóstico de entrega de chaves são:

* Consulte o número de licenças entregues pelo tipo DRM.
* Veja o número de licenças entregues por política.
* Consulte erros por DRM ou tipo de política.
* Consulte o número de pedidos de licença não autorizados dos clientes.

### <a name="example"></a>Exemplo

Ver [Como monitorizar os registos de diagnóstico](media-services-diagnostic-logs-howto.md)do Serviço de Media .

## <a name="next-steps"></a>Passos seguintes

* [Como recolher e consumir dados de registo dos seus recursos Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Criar, ver e gerir alertas de métricas com o Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Como monitorizar as métricas dos Serviços de Media](media-services-metrics-howto.md)
* [Como monitorizar os registos de diagnóstico do Serviço de Media](media-services-diagnostic-logs-howto.md)
