---
title: Monitorizar métricas de serviços de multimédia do Azure e os registos de diagnóstico através do Azure Monitor | Documentos da Microsoft
description: Este artigo fornece uma visão geral de como monitorizar as métricas de serviços de multimédia do Azure e os registos de diagnóstico através do Azure Monitor.
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
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806003"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorizar métricas de serviços de multimédia e registos de diagnóstico

[O Azure Monitor](../../azure-monitor/overview.md) permite-lhe monitorizar as métricas e registos de diagnóstico que o ajudam a compreende o desempenho das suas aplicações. Todos os dados recolhidos pelo Monitor do Azure se encaixa em um dos dois tipos fundamentais, métricas e registos. Pode monitorizar os registos de diagnóstico de serviços de multimédia e criar alertas e notificações para os registos e métricas recolhidas. Pode visualizar e analisar os dados de métricas com [Explorador de métricas](../../azure-monitor/platform/metrics-getting-started.md). Pode enviar registos para [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/)e exportá-las para [do Log Analytics](https://azure.microsoft.com/services/log-analytics/), ou utilize o 3º serviços de terceiros.

Para obter uma descrição detalhada, consulte [monitorizar as métricas do Azure](../../azure-monitor/platform/data-platform.md) e [registos de diagnóstico do Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

Este tópico aborda suportado [métricas de serviços de multimédia](#media-services-metrics) e [registos de diagnóstico de serviços de multimédia](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Métricas de serviços de multimédia

Métricas são recolhidas em intervalos regulares, independentemente do valor é alterado. Eles são úteis para alertar porque eles podem ser convertidos com frequência, e um alerta pode ser acionado rapidamente com a lógica relativamente simples. Para obter informações sobre como criar alertas de métricas, veja [criar, ver e gerir alertas de métricas através do Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Serviços de multimédia suportam as métricas de monitorização para os seguintes recursos:

* Conta
* Ponto final de transmissão em fluxo
 
### <a name="account"></a>Conta

Pode monitorizar as métricas seguintes da conta. 

|Nome da métrica|Display name|Descrição|
|---|---|---|
|AssetCount|Contagem de recursos|Ativos na sua conta.|
|AssetQuota|Quota do recurso|Quota de recursos na sua conta.|
|AssetQuotaUsedPercentage|Percentagem de quota utilizada do recurso|A percentagem da quota de recurso já utilizado.|
|ContentKeyPolicyCount|Contagem de políticas de chave de conteúdo|Diretivas de chave de conteúdo na sua conta.|
|ContentKeyPolicyQuota|Quota de política de chave de conteúdo|Quota de políticas de chaves do conteúdo na sua conta.|
|ContentKeyPolicyQuotaUsedPercentage|Quota de política de chave de conteúdo utilizado percentagem|A percentagem da quota de política de chave de conteúdo já utilizado.|
|StreamingPolicyCount|Contagem de políticas de transmissão em fluxo|Políticas de transmissão em fluxo na sua conta.|
|StreamingPolicyQuota|Quota de política de transmissão em fluxo|Quota de políticas na sua conta de transmissão em fluxo.|
|StreamingPolicyQuotaUsedPercentage|Quota de política de transmissão em fluxo utilizada percentagem|A percentagem da quota de transmissão em fluxo política já utilizado.|
 
Também deverá rever [quotas e limitações da conta](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Ponto final de transmissão em fluxo

Os seguintes serviços de multimédia [pontos finais de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingendpoints) métricas são suportadas:

|Nome da métrica|Display name|Descrição|
|---|---|---|
|Pedidos|Pedidos|Fornece o número total de pedidos HTTP servido pelo ponto final de transmissão em fluxo.|
|Saída|Saída|O número total de bytes de saída. Por exemplo, bytes transmitidos pelo ponto final de transmissão em fluxo.|
|SuccessE2ELatency|Latência de ponto a ponto com êxito|Duração de tempo do que o ponto final de transmissão em fluxo recebeu o pedido para quando o último byte de resposta foi enviado.|

### <a name="why-would-i-want-to-use-metrics"></a>Por que motivo devo utilizar métricas? 

Seguem-se exemplos de como os serviços de multimédia métricas de monitorização podem ajudar a compreender o desempenho das suas aplicações. Algumas perguntas que podem ser resolvidas com a métrica de serviços de multimédia são:

* Como posso monitorizar o meu padrão de transmissão em fluxo ponto final de saber quando eu excedeu os limites?
* Como posso saber se tenho o suficiente unidades de escala do ponto de final de transmissão em fluxo Premium? 
* Como posso definir um alerta para saber quando aumentar verticalmente meus pontos finais de transmissão em fluxo?
* Como posso definir um alerta de saber quando foi atingida a máxima saída configurada na conta?
* Como posso ver a divisão de pedidos a falhar e o que está causando a falha?
* Como posso ver o número de pedidos HLS ou travessão está sendo obtido do empacotador?
* Como posso definir um alerta para saber quando foi atingido o valor de limiar de n. º de pedidos falhados? 

### <a name="example"></a>Exemplo

Consulte [como monitorizar as métricas de serviços de multimédia](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Registos de diagnóstico de serviços de multimédia

Os registos de diagnóstico fornecem dados avançados e frequentes sobre o funcionamento de um recurso do Azure. Para obter mais informações, consulte [como recolher e consumir dados de registo dos seus recursos do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Os Media Services suportam os seguintes registos de diagnóstico:

* Entrega de chave

### <a name="key-delivery"></a>Entrega de chave

|Nome|Descrição|
|---|---|
|Pedido de serviço de entrega de chave|Registos que mostram as informações de pedido de serviço de entrega de chave. Para obter mais detalhes, consulte [esquemas](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Por que motivo devo utilizar registos de diagnóstico? 

Algumas coisas que pode examinar com registos de diagnóstico de entrega de chave são:

* Ver o número de licenças entregues por tipo DRM
* Ver o número de licenças pela política 
* Consulte os erros por tipo de política ou de DRM
* Ver o número de pedidos de licença não autorizadas de clientes

### <a name="example"></a>Exemplo

Consulte [como monitorizar os registos de diagnóstico do serviço de multimédia](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>Passos Seguintes 

* [Como recolher e consumir dados de registo dos seus recursos do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Criar, ver e gerir alertas de métricas através do Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Como monitorizar as métricas de serviços de multimédia](media-services-metrics-howto.md)
* [Como monitorizar os registos de diagnóstico do serviço de multimédia](media-services-diagnostic-logs-howto.md)
