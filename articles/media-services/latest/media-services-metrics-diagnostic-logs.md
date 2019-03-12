---
title: Monitorizar métricas de serviços de multimédia e registos de diagnóstico através do Azure Monitor | Documentos da Microsoft
description: Este artigo fornece uma visão geral de como monitorizar as métricas de serviços de multimédia e registos de diagnóstico através do Azure Monitor.
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
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: 4252b0f26ef3c02216a41dde5d2e1cb5ea0efd6a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555070"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorizar métricas de serviços de multimédia e registos de diagnóstico

[O Azure Monitor](../../azure-monitor/overview.md) permite-lhe monitorizar as métricas e registos de diagnóstico que o ajudam a compreende o desempenho das suas aplicações. Todos os dados recolhidos pelo Monitor do Azure se encaixa em um dos dois tipos fundamentais, métricas e registo. Pode monitorizar os registos de diagnóstico de serviços de multimédia e criar alertas e notificações para os registos e métricas recolhidas. Pode visualizar e analisar os dados de métricas com [Explorador de métricas](../../azure-monitor/platform/metrics-getting-started.md). Pode enviar registos para [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/)e exportá-las para [do Log Analytics](https://azure.microsoft.com/services/log-analytics/), ou utilize o 3º serviços de terceiros.

Para uma visão geral detalhada, consulte [monitorizar as métricas do Azure](../../azure-monitor/platform/data-collection.md) e [registos de diagnóstico do Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

Este tópico aborda atualmente disponíveis [métricas de serviços de multimédia](#media-services-metrics) e [registos de diagnóstico de serviços de multimédia](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Métricas de serviços de multimédia

Métricas são recolhidas em intervalos regulares, independentemente do valor é alterado. Eles são úteis para alertar porque eles podem ser convertidos com frequência, e um alerta pode ser acionado rapidamente com a lógica relativamente simples.

Atualmente, os seguintes serviços de multimédia [pontos finais de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingendpoints) métricas são emitidas pelo Azure:

|Name|Descrição|
|---|---|
|Pedidos|Fornece detalhes em torno de n. º total de pedidos servidos pelo ponto final de transmissão em fluxo.|
|Saída|Número total de bytes de saída. Por exemplo, bytes transmitidos em fluxo por ponto final de transmissão em fluxo.|
|Latência de ponto a ponto com êxito| Fornece informações sobre a latência de ponto a ponto dos pedidos com êxito.|

Por exemplo, para obter métricas de "Saída" com a CLI, executaria o seguinte `az monitor metrics` comando da CLI:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter informações sobre como criar alertas de métricas, veja [criar, ver e gerir alertas de métricas através do Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Registos de diagnóstico de serviços de multimédia

Atualmente, pode obter os seguintes registos de diagnóstico:

|Name|Descrição|
|---|---|
|Pedido de serviço de entrega de chave|Registos que mostram as informações de pedido de serviço de entrega de chave. Para obter mais detalhes, consulte [esquemas](media-services-diagnostic-logs-schema.md).|

Para ativar o armazenamento de registos de diagnóstico numa conta de armazenamento, deve executar o seguinte `az monitor diagnostic-settings` comando da CLI: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Por exemplo:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Passos Seguintes 

[Como recolher e consumir dados de registo dos seus recursos do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
