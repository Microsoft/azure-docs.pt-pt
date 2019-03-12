---
title: Registos de serviços de multimédia do Azure, diagnóstico de esquemas - Azure
description: Este artigo mostra os esquemas de registos de diagnóstico de serviços de multimédia do Azure.
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
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 225bc4368d2a87fa5fef3f74624fd10f42dc7c97
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555064"
---
# <a name="diagnostic-logs-schemas"></a>Esquemas de registos de diagnóstico

[O Azure Monitor](../../azure-monitor/overview.md) permite-lhe monitorizar as métricas e registos de diagnóstico que o ajudam a compreende o desempenho das suas aplicações. Pode monitorizar os registos de diagnóstico de serviços de multimédia e criar alertas e notificações para os registos e métricas recolhidas. Pode enviar registos para [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/)e exportá-las para [do Log Analytics](https://azure.microsoft.com/services/log-analytics/), ou utilize o 3º serviços de terceiros.

Para obter informações detalhadas, consulte [monitorizar as métricas do Azure](../../azure-monitor/platform/data-collection.md) e [registos de diagnóstico do Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

Este artigo descreve os esquemas de registos de diagnóstico de serviços de multimédia.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de registos de diagnóstico de nível superior

Para obter descrição detalhada do esquema de registos de diagnóstico de nível superior, consulte [suportada serviços, os esquemas e categorias para os registos de diagnóstico do Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Esquema de entrega de chave do registo

### <a name="properties"></a>Propriedades

Estas propriedades são específicas para o esquema de entrega de chave do registo.

|Name|Descrição|
|---|---|
|keyId|O ID da chave do pedido.|
|keyType|Poderia ser um dos seguintes valores: "Limpar" (sem encriptação), "FairPlay", "PlayReady" ou "Widevine".|
|policyName|O nome do Azure Resource Manager da política.|
|tokenType|O tipo de token.|
|statusMessage|A mensagem de estado.|

### <a name="examples"></a>Exemplos

Propriedades do esquema de pedidos de entrega de chave.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Passos Seguintes

[Monitorizar métricas de serviços de multimédia e registos de diagnóstico](media-services-metrics-diagnostic-logs.md)
